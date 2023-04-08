# Dev Notes
## Criteria for car servicing
Whether or not a Lyft rental car should be serviced depends on two factors at the moment:
* The engine; and
* The battery.

Each of the three types of engines has its own criteria for determining when it should be serviced. The same applies to each type of battery.

The current service criteria are as follows:

| Engine/Battery    | Service Criteria                      |
| ----------------- | ------------------------------------- |
| Capulet Engine    | Once every 30,000 miles               |
| Willoughby Engine | Once every 60,000 miles               |
| Sternman Engine   | Only when the warning indicator is on |
| Spindler Battery  | Once every 2 years                    |
| Nubbin Battery    | Once every 4 years                    |

There are five car models in Lyft’s fleet, each with a different engine-battery combination. These are outlined below:

| Car        | Engine            | Battery          |
| ---------- | ----------------- | ---------------- |
| Calliope   | Capulet Engine    | Spindler Battery |
| Glissade   | Willoughby Engine | Spindler Battery |
| Palindrome | Sternman Engine   | Spindler Battery |
| Rorschach  | Willoughby Engine | Nubbin Batte     |
| Thovex     | Capulet Engine    | Nubbin Battery   |

These service criteria will change somewhat frequently in the future, and new car models are bound to be added to the fleet. This is an important consideration throughout the program.

With this in mind, it’s very important that the component is extensible and easy to modify, so new service criteria can be added quickly and efficiently. Just today, you learned that the system must also take tires into account when determining if a car should be serviced in the future.

Tacking this functionality onto the current system would be difficult and messy - instead, you have been instructed to take the time to refactor the codebase prior to making the change. The first step of this process is to draft up a new (clean) system architecture that will allow for the seamless inclusion of the new functionality. Your task is to draft and submit a class diagram that maps out how the system will be reorganized.

## UML Class Diagrams
### My initial attempt
```mermaid
classDiagram
    Engine <|-- CapuletEngine
    Engine <|-- SternmanEngine
    Engine <|-- WilloughbyEngine
    Engine : -int current_mileage
    Engine : -int last_service_date
    Engine : -bool warning_light_is_on
    Engine : +engine_needs_service() bool

    Battery <|-- SpindlerBattery
    Battery <|-- NubbinBattery
    Battery : -int last_service_date
    Battery : +battery_needs_service() bool

    Car <|-- Engine
    Car <|-- Battery
    Car: +engine_needs_service() bool
    Car: +battery_needs_service() bool
    Car: + needs_service() bool
    Car <| -- Calliope
    Car <| -- Glissade
    Car <| -- Palindrome
    Car <| -- Rorschach
    Car <| -- Thovex

    class CapuletEngine{
        -int current_mileage
        -int last_service_mileage
        +engine_should_be_serviced() bool
    }

    class SternmanEngine{
        -bool warning_light_is_on
        +engine_should_be_serviced() bool
    }

    class WilloughbyEngine{
        -int current_mileage
        -int last_service_mileage
        +engine_should_be_serviced() bool
    }

    class SpindlerBattery{
        -int last_service_date
        +battery_needs_serviced() bool
    }

    class NubbinBattery{
        -int last_service_date
        +battery_needs_serviced() bool
    }

    class Calliope{
        -current_mileage int
        -last_service_date int
        -last_service_mileage int
        +needs_service() bool
    }

    class Glissade{
        -current_mileage int
        -last_service_date int
        -last_service_mileage int
        +needs_service() bool
    }

    class Palindrome{
        -warning_light_is_on bool
        -last_service_date int
        +needs_service() bool
    }

    class Rorschach{
        -current_mileage int
        -last_service_date int
        -last_service_mileage int
        +needs_service() bool
    }

    class Thovex{
        -current_mileage int
        -last_service_date int
        -last_service_mileage int
        +needs_service() bool
    }
```

### The solution from Forage
```mermaid
classDiagram
    Car *-- Engine 
    Car *-- Battery
    Car : -engine Engine
    Car : -battery Battery
    Car : +needs_service() bool

    CarFactory --> Car

    Serviceable <|.. Car
    Serviceable : +needs_service() bool

    Battery : +needs_service() bool   
    Battery <|.. SpindlerBattery
    Battery <|.. NubbinBattery

    SpindlerBattery : -last_service_date date
    SpindlerBattery : -current_date date
    SpindlerBattery : +needs_service() bool
    NubbinBattery : -last_service_date date
    NubbinBattery : -current_date date
    NubbinBattery : +needs_service() bool

    Engine : +needs_service() bool
    Engine <|.. CapuletEngine
    Engine <|.. SternmanEngine
    Engine <|.. WilloughbyEngine

    CapuletEngine : -last_service_mileage int
    CapuletEngine : -current_mileage int
    CapuletEngine : +needs_service() bool
    WilloughbyEngine : -last_service_mileage int
    WilloughbyEngine : -current_mileage int
    WilloughbyEngine : +needs_service() bool
    SternmanEngine : -warning_light_is_on bool
    SternmanEngine : +needs_service() bool

    class CarFactory{
        +create_calliope(current_date: date, last_service_date: date, current_mileage: int, last_service_mileage: int) Car
        +create_glissade(current_date: date, last_service_date: date, current_mileage: int, last_service_mileage: int) Car
        +create_palindrome(current_date: date, last_service_date: date, warning_light_is_on: bool) Car
        +create_rorschach(current_date: date, last_service_date: date, current_mileage: int, last_service_mileage: int) Car
        +create_thovex(current_date: date, last_service_date: date, current_mileage: int, last_service_mileage: int) Car
    }
```

