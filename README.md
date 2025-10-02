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
/**
     * Преобразование данных теплотехнического прогноза в табличный формат
     *
     * @param dto DTO с данными о теплотехническом прогнозе
     * @return Список данных для таблицы
     */
    private List<Map<String, String>> getThermalEngineeringForecastTableData(ThermalEngineeringForecastExportDto dto) {
        List<Map<String, String>> tableData = new ArrayList<>();

        if (
                dto.getDepths() != null && !dto.getDepths().isEmpty()
                        && dto.getDateMeasTemp() != null
                        && dto.getDateFirstWintEndTemp() != null
                        && dto.getDateExpBeginTemp() != null
                        && dto.getDateExpEndTemp() != null
        ) {
            SimpleDateFormat formatter = new SimpleDateFormat("dd.MM.yyyy");
            String dateMeasTemp = formatter.format(dto.getDateMeasTemp());
            String dateFirstWintEndTemp = formatter.format(dto.getDateFirstWintEndTemp());
            String dateExpBeginTemp = formatter.format(dto.getDateExpBeginTemp());
            String dateExpEndTemp = formatter.format(dto.getDateExpEndTemp());

            Map<String, String> thermalEngineeringForecastRow = new HashMap<>();

            thermalEngineeringForecastRow.put("${column1}", "");
            thermalEngineeringForecastRow.put("${column2}", dateMeasTemp);
            thermalEngineeringForecastRow.put("${column3}", dateFirstWintEndTemp);
            thermalEngineeringForecastRow.put("${column4}", dateExpBeginTemp);
            thermalEngineeringForecastRow.put("${column5}", dateExpEndTemp);

            tableData.add(thermalEngineeringForecastRow);

            for (int depthIndex = 0; depthIndex < dto.getDepths().size(); ++depthIndex) {
                Map<String, String> thermalEngineeringForecastRow2 = new HashMap<>();

                thermalEngineeringForecastRow2.put("${column1}", formatDoubleValue(dto.getDepths().get(depthIndex)));
                thermalEngineeringForecastRow2.put("${column2}", formatDoubleValue(dto.getMeasTemp().get(depthIndex)));
                thermalEngineeringForecastRow2.put("${column3}", formatDoubleValue(dto.getFirstWintEndTemp().get(depthIndex)));
                thermalEngineeringForecastRow2.put("${column4}", formatDoubleValue(dto.getExpBeginTemp().get(depthIndex)));
                thermalEngineeringForecastRow2.put("${column5}", formatDoubleValue(dto.getExpEndTemp().get(depthIndex)));

                tableData.add(thermalEngineeringForecastRow2);
            }
        }

        return tableData;
    }
