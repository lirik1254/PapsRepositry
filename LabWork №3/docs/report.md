# 1 Добавить ранее созданные или создать диаграммы контейнеров и компонентов нотации C4 model. 


## Диаграмма контейнеров
![Контейнеры](https://github.com/user-attachments/assets/6670f1d2-8f60-4e82-bf86-741a1f284742)

## Диаграмма компонентов для системы мессенджера
![контейнер для системы мессенджера drawio](https://github.com/user-attachments/assets/dfaae98f-e51a-43d0-8fbf-6a9739e617ca)

## Диаграмма компонентов для системы поиска по видео тегам
![контейнер для поисковой системы drawio](https://github.com/user-attachments/assets/78ad7db0-2517-4c11-afa1-a3eb5c38512b)

# 2 Построить диаграмму последовательностей для выбранного варианта использования

![2) Диаграмма последовательностей](https://github.com/user-attachments/assets/92d58c67-0bd3-4089-8cc4-a1d4495146c9)

# 3 Построить модель БД

![3 Модель БД Untitled](https://github.com/user-attachments/assets/16f98e50-5b37-4022-bcda-afd017ed906c)

# 4 Реализовать требуемый клиентский и серверный код с учетом принципов KISS, YAGNI, DRY и SOLID. Пояснить, каким образом были учтены эти принципы. 

``` java
package com.example.demo;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Entity
@Getter
@Setter
@NoArgsConstructor
class Video {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;                // Уникальный идентификатор
    private String title;           // Название
    private String description;     // Описание
    private String fileUrl;         // Ссылка на файл

    public Video(String title, String description, String fileUrl) {
        this.title = title;
        this.description = description;
        this.fileUrl = fileUrl;
    }
}

package com.example.demo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/videos")
class VideoController {

    private final VideoService videoService;

    @Autowired
    public VideoController(VideoService videoService) {
        this.videoService = videoService;
    }

    @PostMapping
    public ResponseEntity<String> uploadVideo(@RequestBody VideoRequest request) {
        videoService.saveVideo(request);
        return ResponseEntity.ok("Видео загружено");
    }

    @GetMapping
    public ResponseEntity<List<Video>> getAllVideos() {
        return ResponseEntity.ok(videoService.getAllVideos());
    }
}

package com.example.demo;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;


@Repository
interface VideoRepository extends JpaRepository<Video, Long> {}

package com.example.demo;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
class VideoRequest {
    private String title;
    private String description;
    private String fileUrl;
}

package com.example.demo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

@Service
class VideoService {

    private final VideoRepository videoRepository;

    @Autowired
    public VideoService(VideoRepository videoRepository) {
        this.videoRepository = videoRepository;
    }

    @Transactional
    public void saveVideo(VideoRequest request) {
        Video video = new Video(request.getTitle(), request.getDescription(), request.getFileUrl());
        videoRepository.save(video);
    }

    public List<Video> getAllVideos() {
        return videoRepository.findAll();
    }
}

package com.example.demo;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
class VideoRequest {
    private String title;
    private String description;
    private String fileUrl;
}
```

### KISS
Код простой и понятный. Все классы выполняют строго определенные задачи
Используются стандартные практики Spring Framework

### YAGNI
Ненужный код отсутствует, все реализованные методы и классы нужны для выполнения текущей функциональности.

### DRY
Код не содержит дублирования.
Маппинг между VideoRequest и Video вынесен в метод сервиса, что исключает повторяющийся код.

### SOLID

Single Responsibility (Принцип единственной ответственности):

Каждый класс отвечает за свою задачу:
VideoController — управление запросами.
VideoService — бизнес-логика.
VideoRepository — доступ к данным.
VideoRequest — DTO для передачи данных.
Это соответствует SRP.

Open-Closed (Принцип открытости/закрытости):
Логика в сервисе легко расширяется, например, можно добавить обработку ошибок или новую бизнес-логику, не изменяя контроллер.

Liskov Substitution (Принцип подстановки Барбары Лисков):
Все интерфейсы и классы могут быть заменены их реализациями без изменения внешнего поведения.

Interface Segregation (Принцип разделения интерфейса):
Интерфейс VideoRepository достаточно узкий и специфичный.

Dependency Inversion (Принцип инверсии зависимостей):
Сервис использует зависимость от интерфейса VideoRepository, а не конкретной реализации.
