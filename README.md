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
  <v-dialog v-model="isSummaryTableDialogVisible" width="1400px" persistent>
    <v-card>
      <v-card-title class="pa-0 mx-0">
        <DialogHeader
            headerTitle="Сводная таблица"
            @closeDialog="closeSummaryTableDialog()"
        />
      </v-card-title>

      <!--Быстрые фильтры (Chips)-->
      <div class="pa-4">
        <v-chip-group
            multiple
            column
            active-class="custom-active-chip"
            v-model="selectedFilters"
            @change="applyFilters"
        >
          <v-chip class="custom-chip" value="calculations" filter>
            Расчеты
          </v-chip>

          <v-chip class="custom-chip" value="dm" filter>
            ДМ
          </v-chip>

          <v-chip class="custom-chip" value="ts" filter>
            ТС
          </v-chip>
        </v-chip-group>
      </div>

      <div>
        <!--Сводная таблица-->
        <v-data-table
            :headers="tableHeaders"
            :items="deviationStructuresItems"
            :server-items-length="serverItemsLength"
            :items-per-page="itemsPerPage"
            :page="currentPage"
            :loading="loading"
            :multi-sort="false"
            :sort-by="sortBy"
            :sort-desc="sortDesc"
            :must-sort="true"
            :custom-sort="customSort"
            hide-default-header
            class="table-gtm-elements mr-10 ml-10"
            :footer-props="{
              itemsPerPageOptions: perPageItems,
              showCurrentPage: true,
              showFirstLastPage: true,
            }"
            @update:items-per-page="changeItemsPerPage"
            @update:page="changePage"
            @update:sort-by="changeSortBy"
            @update:sort-desc="changeSortDesc"
        >
          <template v-slot:header="{ props: { headers } }">
            <thead>
              <tr>
                <th
                    v-for="(header, i) in headers"
                    :key="i"
                    :width="header.width"
                    class="px-0 header-cell"
                    @click="header.sortable ? changeSort(header) : null"
                >
                  <div class="header-container"
                       :class="[
                           header.sortable ? 'sortable' : 'un-sortable',
                           sortDesc ? 'desc' : 'asc',
                           header.value === sortBy ? 'active' : 'de-active'
                       ]"
                  >
                    <v-tooltip bottom v-if="header.tooltip">

                      <template v-slot:activator="{ on }">
                        <span v-on="on">
                          {{
                            header.text
                          }}
                        </span>
                      </template>

                      <span>
                        {{
                          header.text
                        }}
                      </span>

                    </v-tooltip>

                    <div v-else>
                      {{
                        header.text
                      }}
                    </div>

                    <v-icon class="icon-sort" small v-if="header.sortable">
                      $mdiArrowUpThin
                    </v-icon>
                  </div>
                </th>
              </tr>
            </thead>
          </template>

          <!--Месторождение-->
          <template v-slot:item.licenseAreaName="{ item }">
            <div>
              {{
                item.licenseAreaName === null
                    ? "-"
                    : item.licenseAreaName
              }}
            </div>
          </template>

          <!--Площадка-->
          <template v-slot:item.projectName="{ item }">
            <div>
              {{
                item.projectName === null
                    ? "-"
                    : item.projectName
              }}
            </div>
          </template>

          <!--Объект-->
          <template v-slot:item.nodeName="{ item }">
            <div>
              <a
                  v-if="item.nodeName && item.objectId"
                  href="#"
                  class="object-link"
                  @click.prevent="navigateToObject(item)"
              >
                {{
                  item.nodeName
                }}
              </a>

              <span v-else-if="item.nodeName">
                {{
                  item.nodeName
                }}
              </span>

              <span v-else></span>
            </div>
          </template>

          <!--Расчеты-->
          <template v-slot:item.lastPileCalcStatus="{ item }">
            <div>
              <v-icon v-if="item.lastPileCalcStatus === true" color="success">
                mdi-check
              </v-icon>

              <v-icon v-else-if="item.lastPileCalcStatus === false" color="error">
                mdi-close
              </v-icon>

              <span v-else></span>
            </div>
          </template>

          <!--ДМ-->
          <template v-slot:item.deformationMarkStatus="{ item }">
            <div>
              <v-icon v-if="item.deformationMarkStatus === true" color="success">
                mdi-check
              </v-icon>

              <v-icon v-else-if="item.deformationMarkStatus === false" color="error">
                mdi-close
              </v-icon>

              <span v-else></span>
            </div>
          </template>

          <!--ТС-->
          <template v-slot:item.thermometricWellStatus="{ item }">
            <div>
              <v-icon v-if="item.thermometricWellStatus === true" color="success">
                mdi-check
              </v-icon>

              <v-icon v-else-if="item.thermometricWellStatus === false" color="error">
                mdi-close
              </v-icon>

              <span v-else></span>
            </div>
          </template>
        </v-data-table>
      </div>

      <!--Закрытие окна таблицы-->
      <v-card-actions class="shadow-for-card-actions">
        <v-spacer/>
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
import { mapMutations } from "vuex";
import routesUtils from "@/assets/js/utils/routesUtils";

export default {
  name: "SummaryTableDialog",
  components: {
    DialogHeader,
  },
  data() {
    return {
      tableHeaders: [
        {
          text: "Месторождение",
          value: "licenseAreaName",
          width: "15%",
          align: "center",
          class: "pl-6",
          sortable: true
        },
        {
          text: "Площадка",
          value: "projectName",
          width: "15%",
          align: "center",
          sortable: true
        },
        {
          text: "Объект",
          value: "nodeName",
          width: "15%",
          align: "center",
          sortable: true
        },
        {
          text: "Расчеты",
          value: "lastPileCalcStatus",
          width: "15%",
          align: "center",
          tooltip: "Состояние последнего завершенного свайного расчета",
          sortable: true
        },
        {
          text: "ДМ",
          value: "deformationMarkStatus",
          width: "15%",
          align: "center",
          tooltip: "Статус по деформационным маркам",
          sortable: true
        },
        {
          text: "ТС",
          value: "thermometricWellStatus",
          width: "15%",
          align: "center",
          tooltip: "Статус по термометрическим скважинам",
          sortable: true
        },
      ],
      perPageItems: [10, 20, 40, 80],
      serverItemsLength: 0,
      itemsPerPage: 10,
      currentPage: 1,
      deviationStructuresItems: [],
      loading: false,
      sortBy: "licenseAreaName",
      sortDesc: false,
      selectedFilters: [],
      filteredItems: []
    };
  },

  mounted() {
    this.getDeviationStructuresItems();
  },

  computed: {
    isSummaryTableDialogVisible: {
      get() {
        return this.$store.state.dialogs.isSummaryTableDialogVisible;
      },
      set(value) {
        this.$store.commit("updateIsSummaryTableDialogVisible", value);
      }
    }
  },

  methods: {
    ...mapMutations(["setSnack"]),

    /**
     * Отключение сортировки v-data-table
     */
    customSort(items, sortBy, sortDesc) {
      return items;
    },

    /**
     * Навигация к объекту во вкладку "Сводная информация"
     */
    navigateToObject(item) {
      const tempCardPath = item && item.categoryId
          ? routesUtils.getRouterCategoryPathByCategoryId(item.categoryId)
          : null;

      const cardPath = tempCardPath;

      this.$router.push(
        routesUtils.routes.CARD +
          cardPath +
          item.objectId +
          "/" +
          routesUtils.routes.SUMMARY
      );

      this.closeSummaryTableDialog();
    },

    /**
     * Получение данных об отклонении структурных элементов
     */
    getDeviationStructuresItems() {
      this.loading = true;

      const filterParams = {};

      if (this.selectedFilters.includes('calculations')) {
        filterParams.lastPileCalcStatus = false;
      }

      if (this.selectedFilters.includes('dm')) {
        filterParams.deformationMarkStatus = false;
      }

      if (this.selectedFilters.includes('ts')) {
        filterParams.thermometricWellStatus = false;
      }

      const url = `/summaryTable/${this.itemsPerPage}/${this.currentPage - 1}/${this.sortBy}/${this.sortDesc}`;

      this.axios
          .get(url, {
            params: filterParams
          })
          .then((response) => {
            const payload = response.data && response.data.data ? response.data.data : response.data;

            if (payload && payload.content) {
              this.deviationStructuresItems = payload.content;
              this.filteredItems = [...payload.content];
              this.serverItemsLength = payload.totalElements || 0;
            } else {
              this.deviationStructuresItems = [];
              this.filteredItems = [];
              this.serverItemsLength = 0;
            }
          })
          .catch((thrown) => {
            console.error('Ошибка при загрузке сводной таблицы: ', thrown);

            this.setSnack({
              message: "Не удалось загрузить данные сводной таблицы",
              color: "error"
            });
          })
          .finally(() => {
            this.loading = false;
          });
    },

    /**
     * Обработка смены страницы
     */
    changePage(page) {
      this.currentPage = page;
      this.getDeviationStructuresItems();
    },

    /**
     * Обработка смены количества элементов страницы
     */
    changeItemsPerPage(itemsPerPage) {
      this.itemsPerPage = itemsPerPage;
      this.currentPage = 1;
      this.getDeviationStructuresItems();
    },

    /**
     * Обработка нажатия по значку сортировки
     */
    changeSort(header) {
      if (this.sortBy === header.value) {
        // Для сортировки по указанному столбцу будем менять направление
        this.sortDesc = !this.sortDesc;
      } else {
        // Для сортировки по новому столбцу будем устанавливаеть его и направление сортировки по умолчанию
        this.sortBy = header.value;
        this.sortDesc = false;
      }

      this.currentPage = 1;
      this.getDeviationStructuresItems();
    },

    /**
     * Обработка смены поля сортировки
     */
    changeSortBy(newSortBy) {
      const value = Array.isArray(newSortBy) ? newSortBy[0] : newSortBy;

      if (value) {
        this.sortBy = value;
        this.currentPage = 1;
        this.getDeviationStructuresItems();
      }
    },

    /**
     * Обработка смены направления сортировки
     */
    changeSortDesc(newSortDesc) {
      const value = Array.isArray(newSortDesc) ? newSortDesc[0] : newSortDesc;

      if (typeof value === 'boolean') {
        this.sortDesc = value;
        this.currentPage = 1;
        this.getDeviationStructuresItems();
      }
    },

    /**
     * Применение фильтров
     */
    applyFilters() {
      this.currentPage = 1;
      this.getDeviationStructuresItems();
    },

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

.v-data-table ::v-deep th {
  border-bottom: none !important;
  height: 44px !important;
  cursor: pointer;
}

.v-data-table ::v-deep td {
  border-bottom: none !important;
}

.close-dialog-btn {
  width: 116px;
  height: 40px;
  color: #393939;
}

.header-cell {
  text-align: center !important;
}

.object-link {
  color: var(--v-info-base);
  text-decoration: underline;
  cursor: pointer;
}

.object-link.hover {
  color: var(--v-primary-base);
  text-decoration: none;
}

// Стили для сортировок
.header-container {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 4px;
  padding: 8px;
  transition: color 0.3s ease;
}

.header-container.sortable {
  cursor: pointer;
  user-select: none;
}

.header-container.sortable.hover {
  color: var(--v-primary-base);
}

.header-container.sortable.active {
  color: var(--v-primary-base);
}

.header-container.un-sortable {
  cursor: default;
}

.header-container.sortable.asc.active .icon-sort {
  transform: rotate(0deg);
  color: var(--v-primary-base);
  opacity: 1;
}

.header-container.sortable.desc.active .icon-sort {
  transform: rotate(180deg);
  color: var(--v-primary-base);
  opacity: 1;
}

.header-container.sortable.de-active .icon-sort {
  opacity: 0.3;
}

.header-container.sortable.de-activ.hover .icon-sort {
  opacity: 0.7;
}

.icon-sort {
  transition: transform 0.3s ease, opacity 0.3s ease;
  font-size: 18px;
}

// Стили для быстрых фильтров (Chips)
::v-deep .v-chip--active.custom-active-chip {
  background-color: #facc2e !important;
  color: #212121 !important;
}

::v-deep .custom-active-chip .v-icon {
  display: none !important;
}

.custom-chip {
  color: var(--v-mainFont-base) !important;
  background-color: var(--v-backgroundSecondary-base) !important;
}

.custom-chip:hover {
  background-color: var(--v-backgroundSecondary-darken1) !important;
}

</style>
