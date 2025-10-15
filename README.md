# Описание содержимого репозитория
# Методичка № 1 включает в себя следующие темы:
- Примитивные типы данных, арифметические и логические операции
- Понятие класс и объект (введение в ООП)
- Ссылочные типы данных
- Перегрузка методов и конструкторов
- Область видимости
- Конструкция if/else
- Оператор множественного выбора switch
- Циклы for, while и do while
- Классы String, StringBuilder
- Массивы
- ArrayList
- Инкапсуляция, интерфейсы и полиморфизм
- Исключения
- Лямбда выражения
- Generics (Дженерики)

# Методичка № 2 включает в себя следующие темы:
- Интерфейсы Comparable и Comparator
- Generics
- Коллекции
- Вложенные классы
- Лямбда-выражения
- Streams
- Многопоточность
- Работа с файлами: IO и NIO
- Регулярные выражения
- Рефлексия

# Методичка № 3 включает в себя следующие темы:
- Рассмотрение IoC и DI
- Аспектно Ориентированное Программирование (AOP)
- Основы Hibernate
- Spring MVC
- Spring MVC + Hibernate + AOP
- Spring REST
- Spring Security
- Spring Boot
# Дополнительно

// Стили для быстрых фильтров (Chips)
.chips-container {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
}

// Используем более специфичные селекторы
.chips-container ::v-deep .v-chip.custom-active-chip {
  background-color: #FFD54F !important;
  color: #333 !important;
  border: 2px solid #FFC107 !important;
}

.chips-container ::v-deep .v-chip.custom-chip {
  color: var(--v-mainFont-base) !important;
  background-color: var(--v-backgroundSecondary-base) !important;
  cursor: pointer;
  transition: all 0.3s ease;
  border: 2px solid transparent !important;
}

.chips-container ::v-deep .v-chip.custom-chip:hover {
  background-color: #E0E0E0 !important;
}

// Альтернативный вариант - если выше не сработает
.chips-container ::v-deep .custom-active-chip {
  background-color: #FFD54F !important;
  color: #333 !important;
  border: 2px solid #FFC107 !important;
}

.chips-container ::v-deep .custom-chip {
  color: var(--v-mainFont-base) !important;
  background-color: var(--v-backgroundSecondary-base) !important;
  cursor: pointer;
  transition: all 0.3s ease;
  border: 2px solid transparent !important;
}

.chips-container ::v-deep .custom-chip:hover {
  background-color: #E0E0E0 !important;
}
