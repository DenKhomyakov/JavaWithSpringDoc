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
Объект должен быть гиперссылкой, кликабельный для перехода. При нажатии происходит переход ко вкладке "Сводная информация".
SummaryTableDialog.vue:
<template>
  <v-dialog v-model="isSummaryTableDialogVisible" width="1400px" persistent>
    <v-card>
      <v-card-title class="pa-0 mx-0">
        <DialogHeader
            headerTitle="Сводная таблица"
            @closeDialog="closeSummaryTableDialog()"
        />
      </v-card-title>

      <div>
        <v-data-table
            :headers="tableHeaders"
            :items="deviationStructuresItems"
            :server-items-length="serverItemsLength"
            :items-per-page="itemsPerPage"
            :page="currentPage"
            :loading="loading"
            disable-sort
            hide-default-header
            class="table-gtm-elements mr-10 ml-10"
            :footer-props="{
              itemsPerPageOptions: perPageItems,
              showCurrentPage: true,
              showFirstLastPage: true,
            }"
            @update:items-per-page="changeItemsPerPage"
            @update:page="changePage"
        >
          <template v-slot:header="{ props: { headers } }">
            <thead>
            <tr>
              <th
                  v-for="(header, i) in headers"
                  :key="i"
                  :width="header.width"
                  class="px-0 header-cell"
              >
                <v-tooltip bottom v-if="header.tooltip">
                  <template v-slot:activator="{ on }">
                    <span v-on="on"> {{
                        header.text
                      }}
                    </span>
                  </template>
                  <span>
                    {{
                      header.tooltip
                    }}
                  </span>
                </v-tooltip>
                <div v-else>
                  {{
                    header.text
                  }}
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
          <template v-slot:item.constructionName="{ item }">
            <div>
              <!-- ToDo: Гиперссылка для перехода на объект-->

              {{
                item.constructionName === null
                    ? "-"
                    : item.constructionName
              }}
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
import {mapMutations} from "vuex";

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
          class: "pl-6"
        },
        {
          text: "Площадка",
          value: "projectName",
          width: "15%",
          align: "center"
        },
        {
          text: "Объект",
          value: "constructionName",
          width: "15%",
          align: "center"
        },
        {
          text: "Расчеты",
          value: "lastPileCalcStatus",
          width: "15%",
          align: "center",
          tooltip: "Состояние последнего завершенного свайного расчета"
        },
        {
          text: "ДМ",
          value: "deformationMarkStatus",
          width: "15%",
          align: "center",
          tooltip: "Статус по деформационным маркам"
        },
        {
          text: "ТС",
          value: "thermometricWellStatus",
          width: "15%",
          align: "center",
          tooltip: "Статус по термометрическим скважинам"
        },
      ],
      perPageItems: [10, 20, 40, 80],
      serverItemsLength: 0,
      itemsPerPage: 10,
      currentPage: 1,
      deviationStructuresItems: [],
      loading: false
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
     * Получение данных об отклонении структурных элементов
     */
    getDeviationStructuresItems() {
      this.loading = true;

      this.axios
          .get(`/summaryTable/${this.itemsPerPage}/${this.currentPage - 1}`)
          .then((response) => {
            const payload = response.data && response.data.data ? response.data.data : response.data;

            if (payload && payload.content) {
              this.deviationStructuresItems = payload.content;
              this.serverItemsLength = payload.totalElements || 0;
            } else {
              this.deviationStructuresItems = [];
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
          })
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

</style>

ObjectCardTabsControl.vue:
<template>
  <div>
    <v-tabs align-with-title v-model="tab" :height="toolbarHeight">
      <v-tabs-slider color="selectedElement" />
      <v-tab v-if="showCalculationsTab" :to="routes.SUMMARY">
        Сводная информация
      </v-tab>
      <v-tab :to="routes.PROPERTIES"> Карточка объекта </v-tab>
      <v-tab v-if="showOperationTab" :to="routes.OPERATIONS">
        Журнал операций
      </v-tab>

      <v-tooltip bottom v-if="isUndergroundPipelineSection">
        <template v-slot:activator="{ on, attrs }">
          <div v-on="on" class="py-3">
            <v-tab
              v-if="showCalculationsTab"
              :to="routes.CALCULATIONS"
              :disabled="isUndergroundPipelineSection"
            >
              Расчеты
            </v-tab>
          </div>
        </template>
        <span>
          Теплотехнический и свайный расчет не доступен для подземного участка
          трубопровода
        </span>
      </v-tooltip>
      <v-tab
        v-if="showCalculationsTab && !isUndergroundPipelineSection"
        :to="routes.CALCULATIONS"
        :disabled="isUndergroundPipelineSection"
      >
        Расчеты
      </v-tab>
    </v-tabs>
    <v-tabs-items v-model="tab">
      <v-tab-item :id="routes.SUMMARY">
        <router-view name="summary" />
      </v-tab-item>
      <v-tab-item :id="routes.PROPERTIES">
        <router-view name="properties" />
      </v-tab-item>
      <v-tab-item :id="routes.OPERATIONS">
        <router-view name="operations" />
      </v-tab-item>
      <v-tab-item :id="routes.CALCULATIONS">
        <router-view name="calculations" />
      </v-tab-item>
    </v-tabs-items>
  </div>
</template>
<script>
import RoutesUtils from "@/assets/js/utils/routesUtils";
import scssVar from "@/styles/exportVarToJs.scss";
import { mapGetters } from "vuex";

import CategoryUtils from "@/assets/js/utils/categoryUtils";
import routesUtils from "@/assets/js/utils/routesUtils";

export default {
  data() {
    return {
      toolbarHeight: scssVar.toolbarHeight,
      tab: null,
      routes: RoutesUtils.routes,
      propertiesObject: null,
      isUndergroundPipelineSection: false,
      operationCategories: [
        CategoryUtils.types.DM,
        CategoryUtils.types.GGDM,
        CategoryUtils.types.PGDM,
        CategoryUtils.types.GR,
        CategoryUtils.types.GS,
        CategoryUtils.types.TS,
        CategoryUtils.types.TSS,
        CategoryUtils.types.TMN,
        CategoryUtils.types.TSG,
        CategoryUtils.types.BUILDINGS,
        CategoryUtils.types.PICKETS,
        CategoryUtils.types.KDM,
      ],
      calculationCategories: [
        CategoryUtils.types.BUILDINGS,
        CategoryUtils.types.PICKETS,
      ],
    };
  },

  watch: {
    treeItems(to, from) {
      this.selectTreeNode();
    },
    openedGtmNetworkElement(to, from) {
      if (to !== from) {
        this.selectTreeNode();
      }
    },
    "$route.params": {
      handler: function (to, from) {
        if (to == from) return;
        if (
          from &&
          to.objectId == from.objectId &&
          to.categoryId == from.categoryId
        )
          return;
        this.getOpenedTreeNodeShortInfo();
        if (this.$route.name === "picketCardObject") {
          this.getIsUndergroundPipelineSection();
        }
      },
      deep: true,
      immediate: true,
    },

    // костыль для правильной работы роута при обновлении страницы "сводная информация"
    selectedTreeNode() {
      if (this.$route.name === "constructionSummary") {
        this.tab = routesUtils.routes.SUMMARY;
      }
    },
  },

  computed: {
    ...mapGetters(["selectedTreeNode", "openedGtmNetworkElement", "treeItems"]),
    objectId: {
      get() {
        return this.$route.params.objectId;
      },
    },

    showOperationTab() {
      return (
        this.selectedTreeNode &&
        this.operationCategories.includes(this.selectedTreeNode.categoryId) &&
        this.$route.path !=
          routesUtils.routes.CARD +
            routesUtils.routes.SG_CARD +
            this.objectId +
            "/" +
            routesUtils.routes.PROPERTIES
      );
    },
    showCalculationsTab() {
      return (
        this.selectedTreeNode &&
        this.calculationCategories.includes(this.selectedTreeNode.categoryId)
      );
    },
  },

  methods: {
    getIsUndergroundPipelineSection() {
      this.axios
        .get(`/picket/isUndergroundPipelineSection/${this.objectId}`)
        .then((response) => {
          this.isUndergroundPipelineSection = response.data.data;
        })
        .catch((thrown) => {
          console.error(thrown.response);
          this.setSnack({
            message:
              thrown.response && thrown.response.data.message
                ? thrown.response.data.message
                : "Не удалось получить данные о участке трубопровода для пикета",
            color: "error",
          });
        });
    },

    getOpenedTreeNodeShortInfo() {
      if (
        !this.$route.params.objectId ||
        this.$route.params.objectId == "undefined"
      )
        return;
      this.$store
        .dispatch("getOpenedGtmNetworkElement", this.$route.params.objectId)
        .then((x) => {});
    },

    selectTreeNode() {
      if (
        this.treeItems.length === 0 ||
        !this.openedGtmNetworkElement ||
        !this.openedGtmNetworkElement.id
      ) {
        return;
      }

      if (
        CategoryUtils.gtmNetworkCategories.indexOf(
          this.openedGtmNetworkElement.categoryId
        ) === -1
      ) {
        this.$store.dispatch("openTreeNodesByObjectId", {
          objectId: this.$route.params.objectId,
          categoryId: this.openedGtmNetworkElement.categoryId,
        });
      } else {
        this.$store.dispatch("openTreeNodesByObjectId", {
          parentId: this.openedGtmNetworkElement.parentId,
          categoryId: this.openedGtmNetworkElement.categoryId,
        });
      }
    },
  },
};
</script>

PropertiesHeader.vue:
<template>
  <v-toolbar
    flat
    :height="toolbarHeight"
    color="headerBackground"
    class="pl-3 pr-7"
  >
    <v-toolbar-title class="mr-auto header-font-color">
      {{ isSummaryTab ? "Сводная информация" : "Свойства объекта" }}
      {{ objectCode ? objectCode : $store.getters.selectedTreeNode.code }}
    </v-toolbar-title>
    <v-toolbar-items>
      <v-row v-if="disableEditMode">
        <div
          class="align-self-center"
          v-if="actionsUtils.isEditePropertiesObject()"
        >
          <v-tooltip bottom v-if="!hideEditButton">
            <span>Редактировать свойства объекта</span>
            <template v-slot:activator="{ on, attrs }">
              <NipiIconBtn
                v-on="on"
                v-bind="attrs"
                color="headerFont"
                class="mx-1"
                @click="activateEditeMode"
              >
                <v-icon v-text="'$mdiCircleEditOutline'" />
              </NipiIconBtn>
            </template>
          </v-tooltip>
        </div>
        <div class="align-self-center">
          <v-tooltip bottom>
            <span>Обновить свойства объекта</span>
            <template v-slot:activator="{ on, attrs }">
              <NipiIconBtn
                v-on="on"
                v-bind="attrs"
                color="headerFont"
                class="mx-1"
                @click="updateObjProperties"
              >
                <v-icon v-text="'$mdiDatabaseRefresh'" />
              </NipiIconBtn>
            </template>
          </v-tooltip>
        </div>
        <div class="align-self-center" v-if="isFindOnMapVisible()">
          <v-tooltip bottom>
            <span>Показать объект на карте</span>
            <template v-slot:activator="{ on, attrs }">
              <NipiIconBtn
                v-on="on"
                v-bind="attrs"
                color="headerFont"
                class="mx-1"
                @click="
                  goToObjectOnMap ? goToObjectOnMap() : defaultGoToObjectOnMap()
                "
              >
                <v-icon v-text="'$mdiMapSearch'" />
              </NipiIconBtn>
            </template>
          </v-tooltip>
        </div>
        <div class="align-self-center" v-if="isCopyEnabled">
          <v-tooltip bottom>
            <span>Копировать объект</span>
            <template v-slot:activator="{ on, attrs }">
              <NipiIconBtn
                v-on="on"
                v-bind="attrs"
                color="headerFont"
                class="mx-1"
                @click="copyObject()"
              >
                <v-icon v-text="'$mdiContentCopy'" />
              </NipiIconBtn>
            </template>
          </v-tooltip>
        </div>
      </v-row>
      <v-row v-else>
        <div class="align-self-center">
          <v-tooltip bottom>
            <span>Сохранить</span>
            <template v-slot:activator="{ on, attrs }">
              <NipiIconBtn
                v-on="on"
                v-bind="attrs"
                color="headerFont"
                class="mx-1"
                @click="saveProperties"
              >
                <v-icon v-text="'$mdiCheck'" />
              </NipiIconBtn>
            </template>
          </v-tooltip>
        </div>
        <div class="align-self-center">
          <v-tooltip bottom>
            <span>Отмена</span>
            <template v-slot:activator="{ on, attrs }">
              <NipiIconBtn
                v-on="on"
                v-bind="attrs"
                color="headerFont"
                class="mx-1"
                @click="cancelEditing"
              >
                <v-icon v-text="'$mdiClose'" />
              </NipiIconBtn>
            </template>
          </v-tooltip>
        </div>
      </v-row>
    </v-toolbar-items>
  </v-toolbar>
</template>
<script>
import scssVar from "@/styles/exportVarToJs.scss";
import { mapGetters } from "vuex";
import RoutesUtils from "@/assets/js/utils/routesUtils.js";
import categoryUtils from "@/assets/js/utils/categoryUtils";
import ActionsUtils from "@/assets/js/utils/actionsUtils";

export default {
  props: {
    disableEditMode: {
      type: Boolean,
      required: true,
    },
    hideEditButton: {
      type: Boolean,
      default: false,
    },

    /**
     * Если хедер у таба "сводная информация"
     */
    isSummaryTab: {
      type: Boolean,
      default: false,
    },
    objectCode: {
      type: String,
      default: null,
    },
    objectTreeNode: {
      type: Object,
      default: null,
    },

    /**
     * Индивидуальная функция перехода на карту,
     * заменяет функция перехода по умолчанию
     */
    goToObjectOnMap: {
      type: Function,
      default: null,
    },
  },
  computed: {
    ...mapGetters(["selectedTreeNode"]),
    isCopyEnabled: {
      get() {
        return (
          this.selectedTreeNode &&
          this.objectTreeNode.categoryId !== categoryUtils.types.SG &&
          this.objectTreeNode.categoryId !== categoryUtils.types.PROJECT &&
          this.objectTreeNode.categoryId !== categoryUtils.types.PIPELINES &&
          this.objectTreeNode.categoryId !==
            categoryUtils.types.PIPELINE_SECTIONS &&
          this.objectTreeNode.categoryId !== categoryUtils.types.PICKETS &&
          this.$store.state.globalConfig.globalConfigDto.isCopyEnabled
        );
      },
    },
  },
  data() {
    return {
      actionsUtils: ActionsUtils,
      toolbarHeight: scssVar.toolbarHeight,
    };
  },

  methods: {
    isFindOnMapVisible() {
      return (
        this.objectTreeNode.categoryId != categoryUtils.types.SG &&
        this.objectTreeNode.categoryId != categoryUtils.types.IGE
      );
    },
    /**
     * Генерация собитя нажатия на кнопку активации режима редактирования
     */
    activateEditeMode() {
      this.$emit("activateEditeMode");
    },

    /**
     * Генерация собитя нажатия на кнопку обновления свойств
     */
    updateObjProperties() {
      this.$emit("updateObjProperties");
    },

    /**
     * Генерация собитя нажатия на кнопку сохранить свойства
     */
    saveProperties() {
      this.$emit("saveProperties");
    },

    /**
     * Генерация собитя нажатия на кнопку отменить редактирование
     */
    cancelEditing() {
      this.$emit("cancelEditing");
    },

    /**
     * Функция перехода к объекту на карте по умолчанию
     * Вызывается если в компонент не передана внешняя функция в пропс goToObjectOnMap
     */
    defaultGoToObjectOnMap() {
      this.$router.push(RoutesUtils.routes.MAP + `${this.objectTreeNode.id}`);
    },

    copyObject() {
      this.$emit("copyObject");
    },
  },
};
</script>
