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
Привет! Мне нужно, чтобы при изменении размеров окна наша ссылка "Скачать результаты прогноза" и икона изменялись вместе с ним, потому что сейчас оно выходит за его пределы, выезжает
ForecastResults.vue:
<template>

  <v-container>

    <!-- <div class="position-container"> -->

      <div>

        <DownArrowIcon size="16"/>

        <v-btn
          @click="downloadForecastResults"
          class="download-doc-btn"
          small
        >
          Скачать результаты прогноза
        </v-btn>

      </div>

    <!-- </div> -->

  </v-container>

</template>

<script>

import scssVar from "@/styles/exportVarToJs.scss";
import { mapMutations } from "vuex";
import DownArrowIcon from "@/components/Icons/DownArrowIcon.vue";
import FileServiceUtil from "@/assets/js/utils/fileServiceUtil";
import { compose } from "ol/transform";

export default {

  components: { DownArrowIcon },
  props: ["forecastId"],

  data() {

    return {

      toolbarHeight: scssVar.toolbarHeight,

      perPageItems: [10, 20, 40, 80],
      itemsPerPage: 10,
      countMarker: 0,
      items: [],

      reliabilityIndicators: {

        forecastResults: [],
        treeNodeResultTracer: null,

      },
    };
  },

  mounted() {
    this.getForecastResults();
  },

  methods: {
    ...mapMutations(["setSnack"]),

    getForecastResults() {

      this.reliabilityIndicators.forecastResults = [];

      this.axios
        .get(`/solv/forecastsolver/forecastResults/${this.forecastId}`)
        .then((response) => {

          if (!response.data.data) {
            return;
          }

          this.reliabilityIndicators.forecastResults = response.data.data;
        })
        .catch((thrown) => {

          console.error(thrown.response);

          if (thrown.response && thrown.response.data.message) {

            this.forecastResults.push({
              error: thrown.response.data.message,
            });
          }
        });
    },

    downloadForecastResults() {
      console.log("sdfopsfdkopsdfk")
      this.axios
        .get(

          `/solv/forecastsolver/initiateDownloadAndGetForecastResults/${this.forecastId}`,

          {
            responseType: "blob",
          }

        )
        .then((response) => {

          this.setSnack({

            message: "Файлы результатов прогноза успешно скачаны",
            color: "success",

          });

          let fileName = FileServiceUtil.getFileNameFromResponse(
            response.headers
          );

          FileServiceUtil.downloadFile(fileName, response.data);

        })
        .catch((thrown) => {

          console.error(thrown.message);

          this.setSnack({

            message: "Не удалось скачать файлы результатов прогноза",
            color: "error",
          });
        });
    },
  },
};

</script>

<style>

.position-container {
  display: flex;
  justify-content: center;
  align-items: center;
}

.download-doc-btn {
  border: none !important;
  background: none !important;
  box-shadow: none;
  text-decoration: underline !important;
  transition: color 0.3s, text-decoration 0.3s;
}

.position-bnt-and-icon {
  max-width: 50%;
  margin-right: -1100px;
  margin-top: -380px;
  display: flex;
  align-items: center;
}

.download-doc-btn:focus,
.download-doc-btn:hover {
  border: none !important;
  background: none !important;
  box-shadow: none;
  outline: none;
  color: rgb(86, 153, 209);
}

.download-doc-btn::before {
  display: none;
}

.download-doc-btn:active::before {
  display: none;
}

</style>
ForecastDetailDialog.vue:
