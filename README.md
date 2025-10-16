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
.custom-active-chip {
  background-color: var(--v-headerBackground-base) !important;
  color: var(--v-headerFont-base) !important;
  opacity: 1 !important;
}

.custom-active-chip::before {
  opacity: 0 !important;
}

.custom-chip {
  color: var(--v-mainFont-base) !important;
  background-color: var(--v-backgroundSecondary-base) !important;
  transition: all 0.3s ease;
}

.custom-chip:hover {
  background-color: var(--v-backgroundSecondary-darken1) !important;
}

.custom-chip {
  border: 3px solid transparent;
  font-weight: 500;
}

.custom-active-chip {
  border: 3px solid var(--v-warning-base) !important;
  box-shadow: 0 2px 4px rgba(250, 204, 46, 0.3) !important;
}
