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
Таблица должна отображать строки по принципу «одно сооружение с отклонением - одна строка».

В таблицу попадают сооружения только с отклонениями, сооружений/пикетов без отклонений здесь быть не должно. 

Если у сооружения вообще нету ни расчета/измерений по ДМ/ ни измерений по ТС то оно не попадает в эту таблицу. 

Если у сооружения нет информации по одному из факторов - просто ставим пробел, не приравниваем к отклонению.
Каждая строка должна содержать следующие колонки:
<template>
  <v-dialog v-model="isSummaryTableDialogVisible" width="1240px" persistent>
    <v-card>
      <v-card-title class="pa-0 mx-0">
        <DialogHeader
          headerTitle="Сводная таблица"
          @closeDialog="closeSummaryTableDialog()"
        />
      </v-card-title>

      // Примерный план работы:
      // По URL бэка получить данные, сохранить их, распарсить в data
      // Лист заголовков для таблицы
      // Добавить галочки и крестики
      // Пагинация
      // По URL открывать карточку конструкции - что надо???


      <v-data-table
        :headers="tableHeaders"
        :items="blanks"
        disable-sort
        hide-default-footer
        disable-pagination
        class="table-gtm-elements pl-5 pr-6 v-data-table"
      >
      </v-data-table>





      <v-card-actions class="shadow-for-card-actions">
        <v-spacer />
        <v-btn
          depressed
          class="close-dialog-btn"
          @click="closeSummaryTableDialog()"
          color="cancelBtn"
        >
          Закрыть
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
import DialogHeader from "@/components/Dialogs/Components/DialogHeader";
import scssVar from "@/styles/exportVarToJs.scss";
import { mapMutations } from "vuex";

export default {
  name: "SummaryTableDialog",
  components: {
    DialogHeader,
  },

  data() {
    return {
      blanks: [],
      toolbarHeight: scssVar.toolbarHeight,

      tableHeaders: [
        {
          text: "Файл",
          value: "fileName",
          class: "pl-6",
        },
        {
          text: "Состояние",
          value: "state",
          align: "center",
          width: "20%",
        },
        {
          text: "Действие",
          value: "actions",
          align: "center",
          width: "5%",
        },
      ],
    };
  },

  computed: {
    isSummaryTableDialogVisible: {
      get() {
        return this.$store.state.dialogs.isSummaryTableDialogVisible;
      },
      set(value) {
        this.$store.commit("updateIsSummaryTableDialogVisible", value);
      },
    },
  },

  methods: {
    ...mapMutations(["setSnack"]),

    /**
     * Закрытие диалогового окна сводной таблицы
     */
    closeSummaryTableDialog() {
      this.isSummaryTableDialogVisible = false;
    },
  },
};
</script>

<style lang="scss" scoped>
.table-gtm-elements ::v-deep tr > td:first-child {
  padding-left: 24px;
}

.table-scroll {
  overflow-y: auto;
  height: 100vh;
}

.link {
  color: var(--v-info-base);
  text-decoration: underline;
}

.link-icon {
  vertical-align: middle;
}

.disabled-link {
  color: rgba(0, 0, 0, 0.26) !important;
  pointer-events: none;
  cursor: default;
  text-decoration: none;
  fill: rgba(0, 0, 0, 0.26) !important;
}

.v-data-table ::v-deep th {
  border-bottom: none !important;
  height: 44px !important;
}

.v-data-table ::v-deep td {
  border-bottom: none !important;
}

.download-btn {
  width: 116px;
  height: 40px;
  color: #fff;
}
</style>



<!--<template>-->
<!--  <v-dialog v-model="dialog" max-width="800px">-->
<!--    <template v-slot:activator="{ on }">-->
<!--      <v-btn color="primary" dark v-on="on">Показать таблицу</v-btn>-->
<!--    </template>-->
<!--    <v-card>-->
<!--      <v-card-title class="headline grey lighten-2">-->
<!--        Итоговая таблица-->
<!--      </v-card-title>-->
<!--      <v-card-text>-->
<!--        <v-data-table-->
<!--          :headers="headers"-->
<!--          :items="items"-->
<!--          :items-per-page="5"-->
<!--          class="elevation-1"-->
<!--        >-->
<!--          <template v-slot:item.action="{ item }">-->
<!--            <v-btn icon small @click="onEdit(item)">-->
<!--              <v-icon small>mdi-pencil</v-icon>-->
<!--            </v-btn>-->
<!--          </template>-->
<!--        </v-data-table>-->
<!--      </v-card-text>-->
<!--      <v-card-actions>-->
<!--        <v-spacer></v-spacer>-->
<!--        <v-btn color="primary" text @click="dialog = false">Закрыть</v-btn>-->
<!--      </v-card-actions>-->
<!--    </v-card>-->
<!--  </v-dialog>-->
<!--</template>-->

<!--<script>-->
<!--export default {-->
<!--  name: "SummaryTable",-->
<!--  data() {-->
<!--    return {-->
<!--      dialog: false,-->
<!--      headers: [-->
<!--        { text: 'ID', value: 'id' },-->
<!--        { text: 'Имя', value: 'name' },-->
<!--        { text: 'Статус', value: 'status' },-->
<!--        { text: '', value: 'action', sortable: false },-->
<!--      ],-->
<!--      items: [-->
<!--        { id: 1, name: 'Объект 1', status: 'Активен' },-->
<!--        { id: 2, name: 'Объект 2', status: 'Неактивен' },-->
<!--        { id: 3, name: 'Объект 3', status: 'Активен' },-->
<!--      ],-->
<!--    };-->
<!--  },-->
<!--  methods: {-->
<!--    onEdit(item) {-->
<!--      // Пример обработчика-->
<!--      this.$emit('edit', item);-->
<!--    },-->
<!--  },-->
<!--};-->
<!--</script>-->

<!--<style scoped>-->
<!--.v-card-title {-->
<!--  font-weight: 600;-->
<!--}-->
<!--</style>-->
