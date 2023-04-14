# Описание требований и архитектуры

## Введение
<!-- Общее краткое описание создаваемой системы -->
В рамках курса осуществляется проектирование решения на основе [постановки задачи от "заказчика"](../../task.md).

- [Описание требований и архитектуры](#описание-требований-и-архитектуры)
  - [Введение](#введение)
  - [Заинтересованные стороны](#заинтересованные-стороны)
  - [Бизнес-контекст (бизнес-требования)](#бизнес-контекст-бизнес-требования)
  - [Глоссарий](#глоссарий)
  - [Модель предметной области](#модель-предметной-области)
  - [Требования к системе](#требования-к-системе)
    - [Сценарии использования (Use case)](#сценарии-использования-use-case)
    - [Функциональные требования](#функциональные-требования)
    - [Нефункциональные требования/Требования к атрибутам качества](#нефункциональные-требованиятребования-к-атрибутам-качества)
    - [Ограничения](#ограничения)
  - [Архитектура](#архитектура)
    - [Журнал архитектурных решений](#журнал-архитектурных-решений)
    - [Контекст решения](#контекст-решения)
    - [Компонентная архитектура](#компонентная-архитектура)
    - [Реализация сценариев использования](#реализация-сценариев-использования)
    - [Программные интерфейсы](#программные-интерфейсы)
    - [Схема развертывания](#схема-развертывания)
  
## Заинтересованные стороны
<!-- Перечень заинтересованных сторон и их интересов по отношению к создаваемой системе. 
Подробнее: https://confluence.mts.ru/pages/viewpage.action?pageId=399975538 
-->
| Заинтересованная сторона | Интересы           |
|:-------------------------|:-------------------|
| *Докладчики*              | *Участие в конференции в качестве эксперта* |
| *Докладчики*              | *Получить обратную связь* |
| *Слушатели*               | *Получить новые знания* |
| *Слушатели*               | *Расширить круг общения в профессиональной среде* |
| *Гос регуляторы*          | *Соблюдение ФЗ "О персональных данных"* |
| *Владелецы/инвесторы*     | *Получить возможность дальнейшей монетизации сервиса* |


## Бизнес-контекст (бизнес-требования)
<!-- Общее описание бизнес-контекста создаваемой системы (автоматизируемой деятельности), список бизнес-целей заинтересованных сторон 
Подробнее: https://confluence.mts.ru/pages/viewpage.action?pageId=399973845
-->
BR.01 Возможноть опубликовать информации о новой конференции
BR.02 Докладчиками на концеренции могут быть только зарегистрированные пользователи
BR.03 Должна быть возможность разместить заявку на выступление с докладом на выбранной конференции
BR.04 Материалы потенциальных докладчиков должны рецензироваться. 
BR.05 Конференция должна содержать программу мероприятия
BR.06 Должна быть возможность посмотреть расписание конференций 
BR.07 Конференция должна транслироваться в режиме реального времени.  
BR.08 Участники конференции должны иметь возможность дать обратную связь по конференеции.  


## Глоссарий
<!-- Содержит основные понятия и термины предметной области  
Подробнее: https://confluence.mts.ru/pages/viewpage.action?pageId=375782595
-->
| Понятие                        | Сокращение                         | Определение                       |
|:-------------------------------|:-----------------------------------|:----------------------------------|
| *Термин, обозначающий понятие* | *Сокращение термина (при наличии)* | *Развернутое определение понятия* |

## [Модель предметной области](data/data.md)

```plantuml
@startuml
' Логическая модель данных в варианте UML Class Diagram (альтернатива ER-диаграмме).
namespace Conference {
  class Conference
  {
    id : long
    title : string
    description : string
    eventStartDate : datetime
    eventEndDate : datetime
    status : string
    conferenceSchedule : ConferenceSchedule
    createDate : datetime
    updateDate : datetime
  }

  enum ConferenceStatus 
  {
    CREATED
    OPENED
    CLOSED
    CANCELLED
  }

  class ConferenceReport
  {
    id : long
    speaker : Speaker
    title : string
    description : string
    conference : Conference
    status : string
    createDate : datetime
    updateDate : datetime 
    likeCount : long
    dislikeCount : long
  }

  enum ReportStatus
  {
    REQUEST
    ONREVIEW
    APPROVED
    DECLINED
  }

  class Speaker 
  {
    id : long
    firstName : String
    lastName : String
    phone : String
    email : String
    company : String
    position : String
    createDate : datetime
    updateDate : datetime
  }

  class Reviewer 
  {
    id : long
    firstName : String
    lastName : String
    createDate : datetime
    updateDate : datetime  
  }

  class ReportReview
  {
    id : long
    report : ConferenceReport
    reviewer : Reviewer
    review : String
    createDate : datetime
    updateDate : datetime  
  }

  class ConferenceProgramItem
  {
    id : long
    confernceReport : ConferenceReport
    startDate : datetime
    endDate : datetime
  }

  class ConferenceSchedule
  {
    id : long
    schedule : [ConferenceProgramItem]
  }

  class ConferenceFeedback
  {
    id : long
    conference : Conference
    isPositive : boolean
    feedback : string
    createDate : datetime
  }

Conference -- ConferenceStatus
ConferenceFeedback *-- "1" Conference
ConferenceReport -- ReportStatus
ConferenceReport -- Speaker
Conference -- ConferenceSchedule
ConferenceProgramItem *-- "1" ConferenceSchedule
ConferenceProgramItem -- ConferenceReport
ReportReview *-- "1" ConferenceReport 
Reviewer -- ReportReview
}
@enduml
``` 

## Требования к системе

### Сценарии использования (Use case)
<!-- Подробное описание сценариев использования системы с привязкой к ролям участников и задействованным бизнес-сущностям 
https://confluence.mts.ru/pages/viewpage.action?pageId=375782108 
https://confluence.mts.ru/pages/viewpage.action?pageId=375782119 
-->
#### Диаграмма сценариев использования (Use Case Diagram) <!-- omit in toc -->

```plantuml
@startuml
left to right direction
actor "Food Critic" as fc
rectangle Restaurant {
  usecase "Eat Food" as UC1
  usecase "Pay for Food" as UC2
  usecase "Drink" as UC3
}
fc --> UC1
fc --> UC2
fc --> UC3
@enduml
```

#### Список сценариев использования <!-- omit in toc -->

| ID     | Описание                                          |
|--------|---------------------------------------------------|
| UC.001 | *[Название сценария использования](uc/uc.001.md)* |

### Функциональные требования
<!-- Описание требований к функциям, реализуемым системой. Требование может быть привязано к сценарию использования или быть общим 
Подробнее: https://confluence.mts.ru/pages/viewpage.action?pageId=375782501 
-->
| ID     | Функциональное требование             |
|--------|---------------------------------------|
| FR.001 | *Описание функционального требования* |

### Нефункциональные требования/Требования к атрибутам качества
<!-- Требования к основным архитектурным характеристикам (атрибутам качества) системы - надежность, масштабируемость, ИБ, и др.
Подробнее: https://confluence.mts.ru/pages/viewpage.action?pageId=375782530
-->
| ID     | Атрибут качества             | Описание требования                       |
|--------|------------------------------|-------------------------------------------|
| QR.001 | *Название атрибута качества* | *Описание требования к атрибуту качества* |

### Ограничения
<!-- Описываются ограничения, оказывающие влияние на архитектуру системы - временные, финансовые, технологические
Подробнее: https://confluence.mts.ru/pages/viewpage.action?pageId=375782592
-->
| ID     | Ограничение            |
|--------|------------------------|
| AC.001 | *Описание ограничения* |

## Архитектура

### Журнал архитектурных решений
<!-- Записи о ключевых принятых архитектурных решениях (ADR) для реализации архитектурно-значимых требований.
Подробнее: https://confluence.mts.ru/pages/viewpage.action?pageId=421162308
-->
- [ADR.NNN Суть решения](adr/adr-template.md)

### [Контекст решения](context/context.md)

### [Компонентная архитектура](components/components.md)

### Реализация сценариев использования
<!-- Реализация сценариев использования на основе взаимодействия компонентов системы и внешних систем/участников.
Диаграммы последовательности (UML Sequence diagram) и текстовое описание.

Подробнее: 
https://confluence.mts.ru/pages/viewpage.action?pageId=399442132
https://confluence.mts.ru/pages/viewpage.action?pageId=399442170
-->
| ID     | Описание                          | Реализация                                    |
|--------|-----------------------------------|-----------------------------------------------|
| UC.001 | *Название сценария использования* | [Реализация сценария](uc-impl/uc.001-impl.md) |

### Программные интерфейсы
<!-- Спецификации публичных API системы и ее компонентов (синхронных, событийных). Создается на основе модели предметной области для реализации сценариев использования. 
  Форматы: OAS/Swagger, GraphQL, AsyncAPI/CloudEvents
-->
| Компонент             | Интерфейс                                      |
|:----------------------|:-----------------------------------------------|
| *Название компонента* | *[Название интерфейса](api/service-name.yaml)* |

### [Схема развертывания](deployment/deployment.md)
