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
<template>
  <v-container ma-0 pa-0 fluid>
    <v-toolbar
      flat
      :height="toolbarHeight"
      color="headerBackground"
      class="pl-2 pr-7"
    >
      <v-toolbar-title class="mr-auto">
        Промежуточные результаты
      </v-toolbar-title>
    </v-toolbar>
    <div class="position-container">
      <v-data-table
        :headers="tableHeaders"
        :items="reliabilityIndicators.intermediateResults"
        :items-per-page="itemsPerPage"
        disable-sort
        class="table-gtm-elements mt-5 ml-5"
        :hide-default-footer="true"
        :hide-default-header="true"
        style="max-width: 50%; margin-right: 200px"
      >
      </v-data-table>
      <DownArrowIcon size="16" />
      <div class="position-btn">
        <v-btn
          @click="downloadIntermediateResults"
          class="download-csv-btn"
          small
        >
          Скачать промежуточные данные расчетов в формате CSV
        </v-btn>
      </div>
    </div>
  </v-container>
</template>
<script>
import scssVar from "@/styles/exportVarToJs.scss";
import { mapMutations } from "vuex";
import DownArrowIcon from "@/components/Icons/DownArrowIcon.vue";
import FileServiceUtil from "@/assets/js/utils/fileServiceUtil";

export default {
  components: { DownArrowIcon },
  props: ["forecastId"],

  data() {
    return {
      toolbarHeight: scssVar.toolbarHeight,
      tableHeaders: [
        {
          value: "jCaption",
          class: "pl-6",
        },
        {
          value: "jValue",
          align: "center",
          width: "8%",
        },
      ],
      perPageItems: [10, 20, 40, 80],
      itemsPerPage: 10,
      countMarker: 0,
      items: [],
      reliabilityIndicators: {
        intermediateResults: [],
        treeNodeResultTracer: null,
      },
    };
  },

  mounted() {
    this.getIntermediateResults();
  },

  methods: {
    ...mapMutations(["setSnack"]),

    getIntermediateResults() {
      this.reliabilityIndicators.intermediateResults = [];

      this.axios
        .get(`/solv/forecastsolver/intermediateResults/${this.forecastId}`)
        .then((response) => {
          if (!response.data.data) {
            return;
          }
          this.reliabilityIndicators.intermediateResults = response.data.data;
        })
        .catch((thrown) => {
          console.error(thrown.response);
          if (thrown.response && thrown.response.data.message) {
            this.intermediateResults.push({
              error: thrown.response.data.message,
            });
          }
        });
    },

    downloadIntermediateResults() {
      this.axios
        .get(
          `/solv/forecastsolver/initiateDownloadAndGetIntermediateResults/${this.forecastId}`,
          {
            responseType: "blob",
          }
        )
        .then((response) => {
          this.setSnack({
            message: "Файлы расчета успешно скачаны",
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
            message: "Не удалось скачать файлы расчета",
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

.download-csv-btn {
  border: none !important;
  background: none !important;
  box-shadow: none;
  text-decoration: underline !important;
  transition: color 0.3s, text-decoration 0.3s;
}

.position-btn {
  max-width: 50%;
  margin-right: 60px;
}

.download-csv-btn:focus,
.download-csv-btn:hover {
  border: none !important;
  background: none !important;
  box-shadow: none;
  outline: none;
  color: rgb(86, 153, 209);
}
.download-csv-btn::before {
  display: none;
}
.download-csv-btn:active::before {
  display: none;
}
</style>
