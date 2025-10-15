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
      <div>
        <v-chip-group
            multiple
            column
            active-class="custom-active-chip"
            v-model="chipKey"
<!--            @change="updatechipsSelection"-->
        >
          <v-chip class="custom-chip" value="">
            Расчеты
          </v-chip>

          <v-chip class="custom-chip" value="">
            ДМ
          </v-chip>

          <v-chip class="custom-chip" value="">
            ТС
          </v-chip>
        </v-chip-group>

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
      sortDesc: false
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
          routesUtils.routes.PROPERTIES
      );

      this.closeSummaryTableDialog();
    },

    /**
     * Получение данных об отклонении структурных элементов
     */
    getDeviationStructuresItems() {
      this.loading = true;

      const url = `/summaryTable/${this.itemsPerPage}/${this.currentPage - 1}/${this.sortBy}/${this.sortDesc}`;

      this.axios
        .get(url)
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
     * Обновление состояния словаря включенных chips
     */
    updateChipsSelection(selectedKey) {
      if (selectedKey == "all") {

      }
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

.header-container.us-sortable {
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
.custom-active-chip {
  background-color: var(--v-headerBackground-base) !important;
  opacity: 1 !important;
}

.custom-active-chip::before {
  opacity: 0 !important;
}

.custom-chip {
  color: var(--v-mainFont-base) !important;
  background-color: var(--v-backgroundSecondary-base) !important;
}

</style>
------
<template>
  <v-dialog
    class="not-scrollable"
    :persistent="!isDisabledEditMode"
    v-model="isVisible"
    @click:outside="clickOutsideDialog"
    max-width="1200px"
    :overlay-opacity="0.7"
  >
    <!-- Лоудер -->
    <NipiLoader
      v-if="loader.isLoading"
      loaderSize="100"
      :indeterminate="loader.indeterminate"
      :overlayOpacity="0.2"
    />
    <!-- Тулбар -->
    <v-toolbar flat dark>
      <v-toolbar-title class="dialog-title-text ml-2">
        Визуальное обследование {{ categoryNameForTitle }}
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn icon @click="isVisible = false">
        <v-icon>mdi-close</v-icon>
      </v-btn>
    </v-toolbar>
    <v-card class="dialog-card not-scrollable">
      <v-form
        class="not-scrollable flex-row-container fill-height"
        ref="form"
        v-model="valid"
        flat
        lazy-validation
      >
        <!-- Колонка 1 -->
        <div class="left-content-column scrollable pa-7">
          <!-- Контент: Информация об объекте -->
          <div class="mb-7">
            <ObjectInfo />
          </div>
          <!-- Контент: Параметры осбледования -->
          <div class="mb-7">
            <div class="header pb-4">Параметры обследования</div>
            <div class="pt-1 pb-4">
              <v-tooltip bottom :disabled="isDatePickerOpened">
                <span> Дата обследования </span>
                <template v-slot:activator="{ on, attrs }">
                  <div v-on="on" v-bind="attrs">
                    <NipiDateInput
                      ref="dateField"
                      label="Дата обследования"
                      :value="operationData.dateInspection"
                      @onChange="dateChange"
                      :disabled="isDisabledEditMode"
                      :max="new Date().toISOString()"
                      required
                      @isOpenChanged="updateIsDatePickerOpened"
                    />
                  </div>
                </template>
              </v-tooltip>
            </div>
            <div>
              <NipiTextField
                label="ФИО проводившего осмотр"
                v-model="operationData.executor"
                :disabled="isDisabledEditMode"
              />
            </div>
          </div>
          <div>
            <div class="header pb-4">Факторы при осмотре</div>
            <div>
              <v-chip-group
                mandatory
                column
                active-class="custom-active-chip"
                v-model="chipKey"
                @change="updateChipsSelection"
              >
                <v-chip class="custom-chip" value="all"> Все </v-chip>
                <v-chip
                  class="custom-chip"
                  v-for="chipKey in Object.keys(chipsStateDict)"
                  :key="chipKey"
                  :value="chipKey"
                >
                  <AddPhotoIcon
                    class="mr-1"
                    v-if="chipKey === 'Добавить фото'"
                  />
                  <template v-if="chipKey === 'Добавить фото'">
                    {{ isDisabledEditMode ? "Фото" : "Добавить фото" }}
                  </template>
                  <template v-else>
                    {{ chipKey }}
                  </template>
                </v-chip>
              </v-chip-group>
            </div>
          </div>
        </div>

        <!-- Колонка 2 -->
        <div class="right-content-column flex-column-container">
          <!-- Контент-контейнер -->
          <v-row align="center" class="block-row">
            <v-col cols="9">
              <h3 v-if="isAllChipSelected" class="ml-10">Все параметры</h3>
            </v-col>
            <v-col cols="2"
              ><div class="button-container ml-5" v-if="isDisabledEditMode">
                <v-btn @click="allParametersClicked()">{{
                  isShowAllParameters
                    ? "Показать выбранные параметры"
                    : "Показать все параметры"
                }}</v-btn>
              </div>
            </v-col>
            <v-col cols="1">
              <NipiIconBtn
                v-if="isDisabledEditMode"
                @click="printVisualInspection"
                ><v-icon v-text="'$mdiPrinter'" />
              </NipiIconBtn>
            </v-col>
          </v-row>
          <div class="flex-body-item scrollable mt-3">
            <div v-for="(isEnabled, chipKey) in chipsStateDict" :key="chipKey">
              <div
                v-if="isAllChipSelected || isEnabled"
                class="block-header"
                :class="{ clickable: isAllChipSelected }"
                @click="isAllChipSelected && toggleExpand(chipKey)"
              >
                <span v-if="chipKey === 'Добавить фото'">
                  {{ isDisabledEditMode ? "Фото" : "Добавить фото" }}
                </span>
                <span v-else>
                  {{ chipKey }}
                </span>
                <v-icon
                  v-if="isAllChipSelected"
                  color="headerFont"
                  class="toggle-expand-button"
                  :class="{ rotate: chipsStateDict[chipKey] }"
                  v-text="'$mdiChevronDown'"
                />
              </div>

              <transition
                @before-enter="beforeEnter"
                @enter="enter"
                @before-leave="beforeLeave"
                @leave="leave"
                :css="isAllChipSelected"
              >
                <!-- Блок "Деформации и дефекты" -->
                <div
                  v-if="showChipBlock(chipKey, 'Деформации и дефекты')"
                  class="fields-block"
                  :class="{ 'no-transition': !isAllChipSelected }"
                >
                  <v-checkbox
                    label="Наличие видимых деформаций фундаментов"
                    :disabled="isDisabledEditMode"
                    v-show="
                      isShowAllParameters ||
                      operationData.visibleDeformationsFoundations
                    "
                    v-model="operationData.visibleDeformationsFoundations"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Дефекты сварных швов"
                    v-show="isShowAllParameters || operationData.defectsOfWelds"
                    :disabled="isDisabledEditMode"
                    v-model="operationData.defectsOfWelds"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Дефекты заклепочных и болтовых соединений"
                    v-show="
                      isShowAllParameters || operationData.connectionDefects
                    "
                    :disabled="isDisabledEditMode"
                    v-model="operationData.connectionDefects"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Наличие дефектов и механических повреждений фундаментов"
                    v-show="
                      isShowAllParameters || operationData.defectsOfFoundations
                    "
                    :disabled="isDisabledEditMode"
                    v-model="operationData.defectsOfFoundations"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Трещины"
                    v-show="isShowAllParameters || operationData.cracks"
                    :disabled="isDisabledEditMode"
                    v-model="operationData.cracks"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Крены"
                    v-show="isShowAllParameters || operationData.lurches"
                    :disabled="isDisabledEditMode"
                    v-model="operationData.lurches"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Прогибы"
                    v-show="isShowAllParameters || operationData.deflections"
                    :disabled="isDisabledEditMode"
                    v-model="operationData.deflections"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Выгибы"
                    v-show="isShowAllParameters || operationData.bulges"
                    :disabled="isDisabledEditMode"
                    v-model="operationData.bulges"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Перекосы"
                    v-show="isShowAllParameters || operationData.warps"
                    :disabled="isDisabledEditMode"
                    v-model="operationData.warps"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Разломы"
                    v-show="isShowAllParameters || operationData.faults"
                    :disabled="isDisabledEditMode"
                    v-model="operationData.faults"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Откопы"
                    v-show="isShowAllParameters || operationData.diggings"
                    :disabled="isDisabledEditMode"
                    v-model="operationData.diggings"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Коррозия элементов и соединений"
                    v-show="
                      isShowAllParameters || operationData.corrosionOfElements
                    "
                    :disabled="isDisabledEditMode"
                    v-model="operationData.corrosionOfElements"
                  >
                  </v-checkbox>
                </div>

                <!-- Блок "Активизация экзогенных процессов" -->
                <div
                  v-if="
                    showChipBlock(chipKey, 'Активизация экзогенных процессов')
                  "
                  class="pt-4"
                  :class="{ 'no-transition': !isAllChipSelected }"
                >
                  <NipiRadioAsButton
                    label="Состояние морозного пучения"
                    @clickItem="
                      (item) =>
                        clickOperationDataState(item, 'stateFrostHeaving')
                    "
                    :isShowAllParameters="isShowAllParameters"
                    :itemsList="listStateFrostHeaving"
                    :initialSelected="operationData.stateFrostHeaving"
                    :isDisabledEditMode="isDisabledEditMode"
                  />
                  <NipiRadioAsButton
                    label="Состояние морозобойного растрескивания"
                    @clickItem="
                      (item) => clickOperationDataState(item, 'stateFrostCrack')
                    "
                    :isShowAllParameters="isShowAllParameters"
                    :itemsList="listStateFrostCrack"
                    :initialSelected="operationData.stateFrostCrack"
                    :isDisabledEditMode="isDisabledEditMode"
                  />
                  <NipiRadioAsButton
                    label="Состояние осадки грунта"
                    @clickItem="
                      (item) =>
                        clickOperationDataState(item, 'stateSedimentation')
                    "
                    :isShowAllParameters="isShowAllParameters"
                    :itemsList="listStateSedimentation"
                    :initialSelected="operationData.stateSedimentation"
                    :isDisabledEditMode="isDisabledEditMode"
                  />
                  <NipiRadioAsButton
                    label="Состояние ветровой эрозии"
                    @clickItem="
                      (item) =>
                        clickOperationDataState(item, 'stateWindErosion')
                    "
                    :isShowAllParameters="isShowAllParameters"
                    :itemsList="listStateWindErosion"
                    :initialSelected="operationData.stateWindErosion"
                    :isDisabledEditMode="isDisabledEditMode"
                  />
                  <NipiRadioAsButton
                    label="Состояние водной эрозии"
                    @clickItem="
                      (item) =>
                        clickOperationDataState(item, 'stateWaterErosion')
                    "
                    :isShowAllParameters="isShowAllParameters"
                    :itemsList="listStateWaterErosion"
                    :initialSelected="operationData.stateWaterErosion"
                    :isDisabledEditMode="isDisabledEditMode"
                  />
                  <NipiRadioAsButton
                    label="Состояние термоэрозии"
                    @clickItem="
                      (item) =>
                        clickOperationDataState(item, 'stateThermalErosion')
                    "
                    :isShowAllParameters="isShowAllParameters"
                    :itemsList="listStateThermalErosion"
                    :initialSelected="operationData.stateThermalErosion"
                    :isDisabledEditMode="isDisabledEditMode"
                  />
                  <NipiRadioAsButton
                    label="Состояние карстообразования"
                    @clickItem="
                      (item) =>
                        clickOperationDataState(item, 'stateKarstFormation')
                    "
                    :isShowAllParameters="isShowAllParameters"
                    :itemsList="listStateKarstFormation"
                    :initialSelected="operationData.stateKarstFormation"
                    :isDisabledEditMode="isDisabledEditMode"
                  />
                  <NipiRadioAsButton
                    label="Состояние солифлюции"
                    @clickItem="
                      (item) =>
                        clickOperationDataState(item, 'stateSoliflution')
                    "
                    :isShowAllParameters="isShowAllParameters"
                    :itemsList="listStateSoliflution"
                    :initialSelected="operationData.stateSoliflution"
                    :isDisabledEditMode="isDisabledEditMode"
                  />
                  <NipiRadioAsButton
                    label="Состояние cуффозии"
                    @clickItem="
                      (item) => clickOperationDataState(item, 'stateSuffosion')
                    "
                    :isShowAllParameters="isShowAllParameters"
                    :itemsList="listStateSuffosion"
                    :initialSelected="operationData.stateSuffosion"
                    :isDisabledEditMode="isDisabledEditMode"
                  />
                </div>

                <!-- Блок "Благоустройство площадки" -->
                <div
                  v-if="showChipBlock(chipKey, 'Благоустройство площадки')"
                  class="fields-block"
                  :class="{ 'no-transition': !isAllChipSelected }"
                >
                  <v-checkbox
                    label="СМР завершены"
                    :disabled="isDisabledEditMode"
                    v-show="isShowAllParameters || operationData.isCompleted"
                    v-model="operationData.isCompleted"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Завершение планировочных работ"
                    v-show="
                      isShowAllParameters ||
                      operationData.planningWorksIsCompleted
                    "
                    :disabled="isDisabledEditMode"
                    v-model="operationData.planningWorksIsCompleted"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Завершение работ по благоустройству"
                    :disabled="isDisabledEditMode"
                    v-show="
                      isShowAllParameters ||
                      operationData.landscapingIsCompleted
                    "
                    v-model="operationData.landscapingIsCompleted"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Складирование мусора на объекте вне зоны специально отведенного места"
                    :disabled="isDisabledEditMode"
                    v-show="
                      isShowAllParameters ||
                      operationData.outsideStorageOfGarbage
                    "
                    v-model="operationData.outsideStorageOfGarbage"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Складирование снега на объекте вне зоны специально отведенного места"
                    :disabled="isDisabledEditMode"
                    v-show="
                      isShowAllParameters || operationData.outsideStorageOfSnow
                    "
                    v-model="operationData.outsideStorageOfSnow"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Складирование оборудования и материалов на объекте вне зоны специально отведенного места"
                    :disabled="isDisabledEditMode"
                    v-show="
                      isShowAllParameters ||
                      operationData.outsideStorageOfEquipment
                    "
                    v-model="operationData.outsideStorageOfEquipment"
                  >
                  </v-checkbox>
                </div>

                <!-- Блок "Подтопления / Стоки" -->
                <div
                  v-if="showChipBlock(chipKey, 'Подтопления / Стоки')"
                  class="fields-block"
                  :class="{ 'no-transition': !isAllChipSelected }"
                >
                  <v-checkbox
                    label="Подтопление территории"
                    :disabled="isDisabledEditMode"
                    v-show="
                      isShowAllParameters || operationData.floodingTerritory
                    "
                    v-model="operationData.floodingTerritory"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Нарушение функционирования ливневого дренажа"
                    :disabled="isDisabledEditMode"
                    v-show="
                      isShowAllParameters ||
                      operationData.stormDrainageDisturbance
                    "
                    v-model="operationData.stormDrainageDisturbance"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Технологические или бытовые стоки в грунт основания"
                    :disabled="isDisabledEditMode"
                    v-show="
                      isShowAllParameters || operationData.drainToFoundation
                    "
                    v-model="operationData.drainToFoundation"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Наличие воды в подпольях"
                    :disabled="isDisabledEditMode"
                    v-show="
                      isShowAllParameters || operationData.waterInUnderground
                    "
                    v-model="operationData.waterInUnderground"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Наличие воды в подвалах"
                    :disabled="isDisabledEditMode"
                    v-show="
                      isShowAllParameters || operationData.waterInBasements
                    "
                    v-model="operationData.waterInBasements"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Временные водотоки, в т.ч. в паводковый период"
                    :disabled="isDisabledEditMode"
                    v-show="
                      isShowAllParameters || operationData.temporaryWatercourses
                    "
                    v-model="operationData.temporaryWatercourses"
                  >
                  </v-checkbox>
                </div>

                <!-- Блок "Дополнительно" -->
                <div
                  v-if="showChipBlock(chipKey, 'Дополнительно')"
                  class="fields-block"
                  :class="{ 'no-transition': !isAllChipSelected }"
                >
                  <v-checkbox
                    label="Закрытие подполий"
                    :disabled="isDisabledEditMode"
                    v-show="
                      isShowAllParameters || operationData.closingOfBasements
                    "
                    v-model="operationData.closingOfBasements"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Открытые в летний период продухи в проветриваемом подполье"
                    :disabled="isDisabledEditMode"
                    v-show="
                      isShowAllParameters || operationData.openingInSummer
                    "
                    v-model="operationData.openingInSummer"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Закрытые в зимний период продухи в проветриваемом подполье"
                    :disabled="isDisabledEditMode"
                    v-show="isShowAllParameters || operationData.closedInWinter"
                    v-model="operationData.closedInWinter"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Нарушение теплового режима заглубленной емкости"
                    :disabled="isDisabledEditMode"
                    v-show="
                      isShowAllParameters || operationData.thermalDisturbance
                    "
                    v-model="operationData.thermalDisturbance"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Наличие фундамента"
                    :disabled="isDisabledEditMode"
                    v-show="isShowAllParameters || operationData.foundation"
                    v-model="operationData.foundation"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Наличие лакокрасочного покрытия"
                    :disabled="isDisabledEditMode"
                    v-show="isShowAllParameters || operationData.paintCoating"
                    v-model="operationData.paintCoating"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Наличие отмостки"
                    :disabled="isDisabledEditMode"
                    v-show="isShowAllParameters || operationData.blindArea"
                    v-model="operationData.blindArea"
                  >
                  </v-checkbox>
                  <v-checkbox
                    label="Отклонение фактических размеров от проектных"
                    :disabled="isDisabledEditMode"
                    v-show="
                      isShowAllParameters ||
                      operationData.deviationFromDesignDimensions
                    "
                    v-model="operationData.deviationFromDesignDimensions"
                  >
                  </v-checkbox>
                </div>

                <!-- Блок "Добавить фото" -->
                <div
                  v-if="showChipBlock(chipKey, 'Добавить фото')"
                  class="fields-block"
                  :class="{ 'no-transition': !isAllChipSelected }"
                >
                  <div class="pt-2 pb-4">
                    <FilePicker
                      :initial-files="operationData.visualInspectionPhotoDto"
                      :server-files="filePickerServerFiles"
                      :local-files="filePickerLocalFiles"
                      :disabled="isDisabledEditMode"
                      @files-changed="handleFilePickerEmit"
                      @server-files-changed="handleFilePickerServerFilesEmit"
                      @local-files-changed="handleFilePickerLocalFilesEmit"
                    />
                  </div>
                </div>
              </transition>
            </div>
          </div>

          <!-- Нижняя панель с кнопками -->
          <v-card-actions class="flex-sticky-footer pr-7">
            <v-spacer></v-spacer>
            <v-btn
              v-if="isDisabledEditMode || !this.operationId"
              class="formatted-text-button cancel-button pl-4 pr-4"
              outlined
              depressed
              @click="clickOutsideDialog"
            >
              Закрыть
            </v-btn>
            <v-btn
              v-if="!this.operationId"
              class="formatted-text-button pl-4 pr-4"
              color="primary"
              depressed
              @click="createMeasurement"
            >
              Создать
            </v-btn>
            <v-btn
              v-if="
                isDisabledEditMode &&
                this.operationId &&
                actionsUtils.isEditOperation()
              "
              class="formatted-text-button pl-3 pr-5"
              color="primary"
              depressed
              @click="activateEditMode"
            >
              <v-icon left>mdi-pencil</v-icon>
              Редактировать
            </v-btn>
            <v-btn
              v-if="!isDisabledEditMode && this.operationId"
              class="formatted-text-button cancel-button pl-4 pr-4"
              outlined
              depressed
              @click="cancelHandler"
            >
              Отмена
            </v-btn>
            <v-btn
              v-if="!isDisabledEditMode && this.operationId"
              class="formatted-text-button pl-4 pr-4"
              color="primary"
              depressed
              @click="updateVisualInspection"
            >
              Сохранить
            </v-btn>
          </v-card-actions>
        </div>
      </v-form>
    </v-card>
  </v-dialog>
</template>

<script>
import validationRules from "@/assets/js/utils/validationRulesUtils";
import confirmUtils from "@/assets/js/utils/confirmUtils";
import _ from "lodash";
import { mapMutations } from "vuex";
import operationHistoryUtils from "@/assets/js/utils/operationHistoryUtils";
import ActionsUtils from "@/assets/js/utils/actionsUtils";
import ObjectInfo from "@/components/Cards/ObjectInfo";
import AddPhotoIcon from "@/components/Icons/AddPhotoIcon.vue";
import FilePicker from "@/components/Cards/VisualInspection/FilePicker.vue";
import NipiRadioAsButton from "../../NipiComponents/NipiRadioAsButton.vue";
import FileServiceUtil from "@/assets/js/utils/fileServiceUtil";

export default {
  props: {
    // пример: ГР
    categoryNameForTitle: {
      type: String,
      default: null,
    },
    // пример: Грунтовый репер(ГР)
    categoryName: {
      type: String,
      default: null,
      required: true,
    },
    visible: {
      type: Boolean,
      default: false,
    },
    operationId: {
      type: String,
      default: null,
    },
    categoryId: {
      type: String,
      default: null,
      required: true,
    },
  },

  components: {
    ObjectInfo,
    AddPhotoIcon,
    FilePicker,
  },

  data() {
    return {
      isShowAllParameters: false,
      selectedOption: 1,
      chipKey: "all",
      actionsUtils: ActionsUtils,
      md: "3",
      xs: "6",
      sm: "6",
      lg: "3",
      xl: "2",
      mdSelect: "2",
      xsSelect: "3",
      smSelect: "3",
      lgSelect: "3",
      xlSelect: "4",
      rules: validationRules,
      valid: true,
      isDisabledEditMode: true,
      disabledSelected: true,
      isChangedOperationDate: false,
      operationData: {
        id: null,
        dateInspection: null,
        executor: null,
        visualInspectionPhotoDto: null,
        stateFrostHeaving: null,
        stateFrostCrack: null,
        stateSedimentation: null,
        stateWindErosion: null,
        stateWaterErosion: null,
        stateThermalErosion: null,
        stateKarstFormation: null,
        stateSoliflution: null,
        stateSuffosion: null,
        isCompleted: null,
        foundation: null,
        paintCoating: null,
        blindArea: null,
        planningWorksIsCompleted: null,
        landscapingIsCompleted: null,
        visibleDeformationsFoundations: null,
        cracks: null,
        lurches: null,
        deflections: null,
        bulges: null,
        faults: null,
        warps: null,
        diggings: null,
        deviationFromDesignDimensions: null,
        defectsOfFoundations: null,
        corrosionOfElements: null,
        defectsOfWelds: null,
        connectionDefects: null,
        outsideStorageOfGarbage: null,
        outsideStorageOfSnow: null,
        outsideStorageOfEquipment: null,
        closingOfBasements: null,
        closedInWinter: null,
        openingInSummer: null,
        thermalDisturbance: null,
        floodingTerritory: null,
        stormDrainageDisturbance: null,
        drainToFoundation: null,
        waterInBasements: null,
        waterInUnderground: null,
        temporaryWatercourses: null,
      },
      listStateFrostHeaving: [],
      listStateFrostCrack: [],
      listStateSedimentation: [],
      listStateWindErosion: [],
      listStateWaterErosion: [],
      listStateThermalErosion: [],
      listStateKarstFormation: [],
      listStateSoliflution: [],
      listStateSuffosion: [],
      copyOfOperationData: null,
      loader: {
        isLoading: true,
        indeterminate: true,
      },
      chipsStateDict: {
        "Деформации и дефекты": false,
        "Активизация экзогенных процессов": false,
        "Подтопления / Стоки": false,
        "Благоустройство площадки": false,
        Дополнительно: false,
        "Добавить фото": false,
      },
      isAllChipSelected: true,
      filePickerData: [],
      isDatePickerOpened: false,
      filePickerServerFiles: [],
      filePickerLocalFiles: [],
    };
  },

  computed: {
    isVisible: {
      get() {
        return this.visible;
      },
      set(newVal) {
        this.$emit("closeDialogVisualInspection", newVal);
      },
    },

    objectInfo: {
      get() {
        return this.$store.getters.openedGtmNetworkElement;
      },
    },

    allChipStatesTrue() {
      const values = Object.values(this.chipsStateDict);
      return values.length > 0 && values.every((value) => value === true);
    },
  },
  watch: {
    isDisabledEditMode() {
      this.isShowAllParameters = !this.isDisabledEditMode;
    },
    operationData() {
      this.isShowAllParameters = !this.isDisabledEditMode;
      if (this.chipKey == "all") {
        this.reExpandAll();
      }
    },
    isShowAllParameters() {
      if (this.chipKey == "all") {
        this.reExpandAll();
      }
    },
  },
  mounted() {
    //если идентификатора есть то режим просмтотра, иначе режим создания
    this.getValueForSelected();
    if (this.operationId) {
      this.getOperationData(this.operationId);
    } else {
      this.isDisabledEditMode = false;
      this.loader.isLoading = false;
    }
  },

  methods: {
    ...mapMutations(["setSnack"]),

    resetFilePickerData() {
      this.filePickerServerFiles = [];
      this.filePickerLocalFiles = [];
    },

    handleFilePickerServerFilesEmit(data) {
      this.filePickerServerFiles = data;
    },

    handleFilePickerLocalFilesEmit(data) {
      this.filePickerLocalFiles = data;
    },

    handleFilePickerEmit(data) {
      this.filePickerData = data;
    },

    reExpandAll() {
      this.expandAll(false);
      this.$nextTick(() => {
        this.expandAll(true);
        this.isAllChipSelected = true;
      });
    },
    printVisualInspection() {
      this.loader.isLoading = true;
      this.axios
        .get(
          `/visualInspectionExport/exportVisualInspectionPassport/?visualInspectionId=${this.operationId}`,
          {
            responseType: "blob",
          }
        )
        .then((response) => {
          if (response.status === 400) {
            this.setSnack({
              message: response.statusInfo.reasonPhrase,
              color: "error",
            });
          } else {
            let fileName = FileServiceUtil.getFileNameFromResponse(
              response.headers
            );
            FileServiceUtil.downloadFile(fileName, response.data);
          }
        })
        .catch((error) => {
          console.error(error.response);
          this.setSnack({
            message:
              error.response && error.response.data.message
                ? error.response.data.message
                : "Не удалось сформировать файл визуального обследования",
            color: "error",
          });
        })
        .finally(() => {
          this.loader.isLoading = false;
        });
    },
    allParametersClicked() {
      this.isShowAllParameters = !this.isShowAllParameters;
    },
    expandAll(state) {
      Object.keys(this.chipsStateDict).forEach((key) => {
        this.toggleExpandTo(key, state);
      });
    },
    toggleExpandTo(key, state) {
      this.chipsStateDict[key] = state;
    },

    /**
     * Получение данных измерения (операции)
     *
     * @param operationId идентификатор измерения
     */
    getOperationData(operationId) {
      this.loader.isLoading = true;
      this.loader.indeterminate = true;
      this.axios
        .get(`/building/operation/${operationId}`)
        .then((response) => {
          this.operationData = response.data.data;
          this.resetFilePickerData();
          this.loader.isLoading = false;
        })
        .catch((thrown) => {
          //отключаем индикацию загрузки элементов
          this.loader.indeterminate = false;
          console.error(thrown.message);
          this.setSnack({
            message: "Не удалось получить визуальное обследование",
            color: "error",
          });
        });
    },

    /**
     * Обработчик активации режима редактирования
     * делаем копию данных и активируем режим редактирования
     */
    activateEditMode() {
      this.copyOfOperationData = _.cloneDeep(this.operationData);
      this.isDisabledEditMode = false;
      this.getValueForSelected();
    },

    /**
     * Получение всех возможных значений для полей select,
     *  при редактирование
     */
    getValueForSelected() {
      this.axios
        .get("/building/operation/values/select")
        .then((response) => {
          this.listStateFrostHeaving =
            response.data.data.listVisualInspectionBuildingState;
          this.listStateFrostCrack =
            response.data.data.listVisualInspectionBuildingState;
          this.listStateSedimentation =
            response.data.data.listVisualInspectionBuildingState;
          this.listStateWindErosion =
            response.data.data.listVisualInspectionBuildingState;
          this.listStateWaterErosion =
            response.data.data.listVisualInspectionBuildingState;
          this.listStateThermalErosion =
            response.data.data.listVisualInspectionBuildingState;
          this.listStateKarstFormation =
            response.data.data.listVisualInspectionBuildingState;
          this.listStateSoliflution =
            response.data.data.listVisualInspectionBuildingState;
          this.listStateSuffosion =
            response.data.data.listVisualInspectionBuildingState;
          this.disabledSelected = false;
        })
        .catch((thrown) => {
          //отключаем возможность редактировать поля select
          this.disabledSelected = true;
          console.error(thrown.message);
          this.setSnack({
            message: "Не удалось получить перечислимые значения полей",
            color: "error",
          });
        });
    },

    /**
     * Обратка изменения даты
     */
    dateChange(date) {
      this.isChangedOperationDate =
        this.operationData.verificationDate !== date;
      this.operationData.dateInspection = date;
    },

    /**
     * Обратчик отмены редактирования или создания
     */
    cancelHandler() {
      let selectedKey = this.chipKey;
      this.$confirm(
        `Вы действительно хотите отменить изменения?`,
        confirmUtils.option.ConfirmEditProperties
      ).then((res) => {
        if (!res) {
          return;
        }
        this.isDisabledEditMode = true;
        this.operationData = _.cloneDeep(this.copyOfOperationData);
        this.copyOfOperationData = {};
        this.resetFilePickerData();
        this.updateChipsSelection(selectedKey);
      });
    },

    /**
     *  Сохранение изменений операции:
     *  отправляет запрос на сохранение и выключает режим редактирования
     */
    updateVisualInspection() {
      let selectedKey = this.chipKey;
      this.operationData.visualInspectionPhotoDto = this.filePickerData;
      if (!this.$refs.form.validate()) {
        this.setSnack({
          message: "Не все значения корректны",
          color: "error",
        });
        return;
      }
      this.$confirm(
        `Вы действительно хотите сохранить визуальное обследование?`,
        confirmUtils.option.ConfirmEditProperties
      ).then((res) => {
        if (!res) {
          return;
        }
        this.loader.isLoading = true;
        this.loader.indeterminate = true;
        this.axios
          .put("/building/operation/", this.operationData)
          .then((response) => {
            this.isDisabledEditMode = true;
            this.$set(this, "operationData", _.cloneDeep(response.data.data));
            this.copyOfOperationData = {};
            this.setSnack({
              message: "Свойства успешно обновлены",
              color: "success",
            });
            if (this.isChangedOperationDate) {
              this.$emit("createdVisualInspection", response.data.data);
            }
            this.resetFilePickerData();
            this.updateChipsSelection(selectedKey);
          })
          .catch((thrown) => {
            console.error(thrown.message);
            this.setSnack({
              message: "Не удалось обновить свойства",
              color: "error",
            });
          })
          .finally(() => {
            this.loader.isLoading = false;
          });
      });
    },

    /**
     * Создание измерения:
     * отправляет запрос на создание и закрывает окно
     */
    createMeasurement() {
      if (operationHistoryUtils.checkEntityForm(this.$refs.form)) {
        this.setSnack({
          message: "Нельзя создать пустое обследование!",
          color: "error",
        });
        return;
      }
      if (!this.$refs.form.validate()) {
        this.setSnack({
          message: "Не все значения корректны",
          color: "error",
        });
        return;
      }
      this.$confirm(
        `Вы действительно хотите сохранить обследование?`,
        confirmUtils.option.ConfirmEditProperties
      ).then((res) => {
        if (!res) {
          return;
        }
        this.operationData.visualInspectionPhotoDto = this.filePickerData;
        this.loader.isLoading = true;
        this.loader.indeterminate = true;
        this.axios
          .post(
            `/building/operation/${this.$route.params.objectId}/${this.$store.getters.currentUserSid}`,
            this.operationData
          )
          .then((response) => {
            this.$emit("createdVisualInspection", response.data.data);
            this.isVisible = false;
            this.setSnack({
              message: "Обследование успешно создано",
              color: "success",
            });
            this.updateChipsSelection("all");
          })
          .catch((thrown) => {
            console.error(thrown.message);
            this.setSnack({
              message: "Не удалось создать обследование",
              color: "error",
            });
          })
          .finally(() => {
            this.loader.isLoading = false;
          });
      });
    },

    /**
     * Обработка события нажатия за пределами диалогового окна
     */
    clickOutsideDialog() {
      if (!this.isDisabledEditMode && this.operationId) {
        this.setSnack({
          message: "Завершите редактирование измерения!",
          color: "error",
        });
      } else if (!this.isDisabledEditMode && !this.operationId) {
        this.$confirm(
          `Вы действительно хотите отменить создание объекта?`,
          confirmUtils.option.ConfirmEditProperties
        ).then((res) => {
          if (!res) {
            return;
          }
          this.isVisible = false;
        });
      } else {
        this.isVisible = false;
      }
    },

    /**
     * Обновление состояния словаря включенных chips
     */
    updateChipsSelection(selectedKey) {
      // Специальная логика для "Все"
      if (selectedKey === "all") {
        Object.keys(this.chipsStateDict).forEach((key) => {
          this.chipsStateDict[key] = true;
        });
        this.isAllChipSelected = true;
      } else {
        // Сброс всех состояний
        Object.keys(this.chipsStateDict).forEach((key) => {
          this.chipsStateDict[key] = false;
        });
        // Обновление состояния в рядовом случае
        this.chipsStateDict[selectedKey] = true;
        this.isAllChipSelected = false;
      }
    },

    /**
     * Проверить, отображается ли блок по ключу и имени блока
     * @param {String} chipKey   ключ чипса
     * @param {String} blockName имя блока
     * @returns true - отображается, false - не отображается
     */
    showChipBlock(chipKey, blockName) {
      return this.chipsStateDict[chipKey] && chipKey === blockName;
    },

    /**
     * Изменить состояние 'Свернуто/Развернуто' блока
     * @param {String} chipKey ключ, имя блока
     */
    toggleExpand(chipKey) {
      this.chipsStateDict[chipKey] = !this.chipsStateDict[chipKey];
    },

    beforeEnter(el) {
      el.style.height = "0";
      el.style.opacity = "0";
      el.style.marginTop = "-8px";
    },

    enter(el) {
      el.style.transition = "all 0.3s ease";
      requestAnimationFrame(() => {
        el.style.height = el.scrollHeight + "px";
        el.style.opacity = "1";
      });
    },

    beforeLeave(el) {
      el.style.height = el.scrollHeight + "px";
      el.style.opacity = "1";
    },

    leave(el) {
      el.style.transition = "all 0.3s ease";
      el.style.opacity = "0";
      el.style.marginTop = "-16px";
      requestAnimationFrame(() => {
        el.style.height = "0";
        el.style.opacity = "0";
      });
    },

    clickOperationDataState(item, propertyName) {
      if (this.operationData[propertyName] !== item) {
        this.operationData[propertyName] = item;
      } else {
        this.operationData[propertyName] = null;
      }
    },

    updateIsDatePickerOpened(isDatePickerOpened) {
      this.isDatePickerOpened = isDatePickerOpened;
    },
  },
};
</script>

<style scoped>
.scrollable {
  overflow-x: hidden;
  overflow-y: auto;
}

.not-scrollable {
  overflow: hidden;
}

.dialog-card {
  height: 83vh;
  width: 100%;
}

.flex-row-container {
  display: flex;
  flex-direction: row;
}

.flex-column-container {
  display: flex;
  flex-direction: column;
}

.fill-height {
  height: 100%;
}

.left-content-column {
  flex-basis: 36%;
  border-right: 1px solid #ddd;
}

.right-content-column {
  flex-basis: 64%;
  display: flex;
  flex-direction: column;
  height: 100%;
}

.flex-body-item.scrollable {
  overflow-y: auto;
  flex: 1 1 auto;
}

.flex-sticky-footer {
  flex-shrink: 0;
  background: white;
  border-top: 1px solid #ddd;
}

.subheader-card {
  color: black;
  border-bottom: 1px solid #888888;
  font-size: 18px;
}

::v-deep .v-chip-group .v-slide-group__content {
  padding: 0 !important;
}

.custom-chip {
  color: var(--v-mainFont-base) !important;
  background-color: var(--v-backgroundSecondary-base) !important;
}

.custom-active-chip {
  background-color: var(--v-headerBackground-base) !important;
  opacity: 1 !important;
}

.custom-active-chip::before {
  opacity: 0 !important;
}

.block-header {
  position: relative;
  padding: 16px 40px;
  background-color: var(--v-backgroundSecondary-base);
  /* настройки шрифта */
  font-weight: 500;
  font-size: 12px;
  line-height: 100%;
  letter-spacing: 2%;
  vertical-align: middle;
  text-transform: uppercase;
  color: var(--v-mainFont-base);
}

.fields-block {
  padding-left: 40px;
  padding-right: 40px;
  padding-top: 16px;
}

.fields-block >>> .v-input {
  margin-top: 0px;
  margin-bottom: 0px;
}

.fields-block >>> .v-label {
  color: var(--v-mainFont-base) !important;
}

.toggle-expand-button {
  transition: transform 0.3s ease-in-out;
  position: absolute;
  right: 16px;
  top: 10px;
}

.toggle-expand-button.rotate {
  transform: rotate(180deg);
}

.clickable {
  cursor: pointer;
}

.no-transition {
  transition: none !important;
  max-height: none !important;
  opacity: 1 !important;
}

.dialog-title-text {
  font-family: Roboto;
  font-weight: 500;
  font-size: 24px;
  letter-spacing: normal;
}

.formatted-text-button {
  text-transform: none; /* Чтобы текст не был в верхнем регистре */
  letter-spacing: normal; /* Нормальный интервал между буквами */
  font-weight: normal; /* Обычный шрифт, не жирный */
}

.cancel-button {
  background: var(--v-cancelBtn-base);
  color: var(--v-mainFont-base);
  border-color: #bdbdbd;
}

.button-container {
  display: flex;
  justify-content: flex-end; /* Aligns items to the end of the flex container */
}

.block-row {
  flex: 0 1 auto !important;
}
</style>
