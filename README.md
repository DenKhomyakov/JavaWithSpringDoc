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
Frontend, SummaryTableDialog.vue:
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
import {mapMutations} from "vuex";
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
          value: "nodeName",
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

      this.axios
          .get(`/summaryTable/${this.itemsPerPage}/${this.currentPage - 1}`)
          .then((response) => {
            console.log(response.data)
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

.object-link {
  color: var(--v-info-base);
  text-decoration: underline;
  cursor: pointer;
}

.object-link.hover {
  color: var(--v-primary-base);
  text-decoration: none;
}

</style>
Backend:
@RestController
@RequestMapping("/api/summaryTable")
@AllArgsConstructor
public class SummaryTableController {

    private final SummaryTableService summaryTableService;

    /**
     * Получить страницу сводной таблицы.
     * Одна строка - одно сооружение с отклонениями. Если нет отклонений или не хватает данных для их подсчета, то
     * сооружение не попадает в таблицу.
     *
     * @param page  Номер страницы
     * @param size  Количество элементов на странице
     * @return      Страница сводной таблицы
     */
    @GetMapping("{size}/{page}")
    public ResponseEntity<?> getSummaryTable(@PathVariable @NotNull(message = "Не передан обязательный параметр номера страницы!") int page,
                                             @PathVariable @NotNull(message = "Не передан обязательный параметр количества элементов на страницы!") int size) {
        return ResponseEntity.ok(summaryTableService.getSummaryTablePage(page, size));
    }
}
@Service
@AllArgsConstructor
public class SummaryTableService {

    private final TreeNodeRepository treeNodeRepository;
    private final SummaryTableRowRepository summaryTableRowRepository;

    private final ReliabilityIndicatorsService reliabilityIndicatorsService;

    /**
     * Получить страницу сводной таблицы.
     * Одна строка - одно сооружение с отклонениями. Если нет отклонений или не хватает данных для их подсчета, то
     * сооружение не попадает в таблицу.
     *
     * @param page  Номер страницы
     * @param size  Количество элементов на странице
     * @return Страница сводной таблицы
     */
    public Page<SummaryTableRowDto> getSummaryTablePage(int page, int size) {
        Page<SummaryTableRow> tableRows = summaryTableRowRepository.findAll(PageRequest.of(page, size));
        return new PageImpl<>(
                tableRows.getContent().stream()
                        .map(row -> {
                            TreeNode treeNode = treeNodeRepository.findById(row.getId())
                                    .orElseThrow(() -> new BusinessException("Не удалось найти treeNode по id: " + row.getId()));
                            return new SummaryTableRowDto(row, treeNode, getLastPileCalcStatus(treeNode));
                        }).collect(Collectors.toList()),
                PageRequest.of(page, size), tableRows.getTotalElements());
    }

    /**
     * Находит состояние последнего свайного расчета конструкции.
     *
     * @param treeNode  treeNode конструкции
     * @return true - все ок, false - есть отклонения, null - не удалось посчитать
     */
    private Boolean getLastPileCalcStatus(TreeNode treeNode) {
        try {
            ResultReliabilityIndicatorsByForecast forecastResult = reliabilityIndicatorsService
                    .getConstructionReliabilityIndicators(treeNode.getObjectId());
            if (forecastResult.getPileLoadsItems().stream()
                    .allMatch(riDto -> Objects.isNull(riDto.getState()))) {
                return null;
            } else {
                return forecastResult.getPileLoadsItems().stream()
                        .noneMatch(x -> x.getState().equals(false));
            }
        } catch (Exception e) {
            return null;
        }
    }
}
/**
 * Строка сводной таблицы.
 * Одна строка соответствует одному сооружению (пикету или площадному сооружению).
 * Значения заполняются во вьюшке на стороне базы данных.
 */
@Entity
@Table(name = "vw_summary_table_row")
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class SummaryTableRow extends BaseId {

    /**
     * Название конструкции
     */
    @Column(name = "name")
    private String name;

    /**
     * Название площадки
     */
    @Column(name = "project_name")
    private String projectName;

    /**
     * Название ЛУ
     */
    @Column(name = "license_area_name")
    private String licenseAreaName;

    /**
     * Состояние по ТС сооружения.
     * Вычисляется во вьюшке в базе по state_id дочерних ТС сооружения.
     * Результат:
     * state_id дочерних ТС отсутствуют -> значение null;
     * хотя бы один state_id = false -> принимает значение false;
     * иначе -> true.
     */
    @Column(name = "thermometric_well_state")
    private Boolean thermometricWellState;

    /**
     * Состояние по ДМ сооружения.
     * Вычисляется во вьюшке в базе по state_id дочерних ДМ сооружения.
     * Результат:
     * state_id дочерних ДМ отсутствуют -> значение null;
     * хотя бы один state_id = false -> принимает значение false;
     * иначе -> true.
     */
    @Column(name = "deformation_mark_state")
    private Boolean deformationMarkState;
}
/**
 * Строка сводной таблицы, соответствующая одной конструкции.
 */
@Getter
@Setter
@NoArgsConstructor
public class SummaryTableRowDto {
    /**
     * ID конструкции в дереве
     */
    @JsonProperty("nodeId")
    private UUID id;

    /**
     * Название конструкции (сооружения или пикета)
     */
    @JsonProperty("nodeName")
    private String name;

    /**
     * objectId конструкции
     */
    @JsonProperty("objectId")
    private UUID objectId;

    /**
     *  categoryId конструкции
     */
    @JsonProperty("categoryId")
    private UUID categoryId;

    /**
     * Название площадки
     */
    @JsonProperty("projectName")
    private String projectName;

    /**
     * Название ЛУ
     */
    @JsonProperty("licenseAreaName")
    private String licenseAreaName;

    /**
     * Состояние последнего свайного расчета.
     * True - все ок, false - есть отклонения, null - нет данных для заполнения.
     */
    @JsonProperty("lastPileCalcStatus")
    private Boolean lastPileCalcStatus;

    /**
     * Статус по ДМ конструкции.
     * True - все ок, false - есть отклонения, null - нет данных для заполнения.
     */
    @JsonProperty("deformationMarkStatus")
    private Boolean deformationMarkStatus;

    /**
     * Статус по ТС конструкции.
     * True - все ок, false - есть отклонения, null - нет данных для заполнения.
     */
    @JsonProperty("thermometricWellStatus")
    private Boolean thermometricWellStatus;

    public SummaryTableRowDto(SummaryTableRow entity, TreeNode treeNode, Boolean lastPileCalcStatus) {
        this.id =  entity.getId();
        this.name = entity.getName();
        this.objectId = treeNode.getObjectId();
        this.categoryId = treeNode.getCategory().getId();
        this.projectName = entity.getProjectName();
        this.licenseAreaName = entity.getLicenseAreaName();
        this.deformationMarkStatus = entity.getDeformationMarkState();
        this.thermometricWellStatus = entity.getThermometricWellState();
        this.lastPileCalcStatus = lastPileCalcStatus;
    }
}
@Repository
public interface SummaryTableRowRepository extends JpaRepository<SummaryTableRow, UUID> {
}

Frontend-пример, DMSummaryListTable.vue:
<template>
  <v-container ma-0 pa-0 fluid>
    <v-data-table
      :show-select="showSelect"
      :headers="dmType == true ? tableHeadersDm : tableHeadersOtherDm"
      :items="items"
      :loading="isLoading"
      :v-model="selected"
      :server-items-length="serverItemsLength"
      :items-per-page="itemsPerPage"
      :multi-sort="false"
      :sort-by="sortBy"
      :sort-desc="sortDesc"
      :must-sort="true"
      :hide-default-header="true"
      class="table-operation table-scroll"
      v-bind:class="{ 'row-cursor': actionUtils.isOpenOperation() }"
      :footer-props="{
        itemsPerPageOptions: perPageItems,
        showCurrentPage: true,
        showFirstLastPage: true,
      }"
      @update:items-per-page="changeItemsPerPage"
      @update:page="changePage"
      @update:sort-by="changeSortBy"
      @update:sort-desc="changeSortDesc"
      @dblclick:row="onDoubleClick"
      ref="dataTable"
    >
      <v-progress-linear
        class="pa-0"
        slot="progress"
        color="headerBackground"
        height="7"
        indeterminate
      />
      <template
        v-slot:header="{ props: { headers, everyItem, someItems }, on }"
      >
        <thead>
          <tr>
            <th
              v-for="(header, i) in headers"
              :key="i"
              :width="header.width"
              :align="header.align"
              class="px-0 header-cells"
              @click="changeSortBy(header)"
            >
              <div v-if="header.value === 'data-table-select'">
                <v-simple-checkbox
                  v-ripple="{ center: true }"
                  class="item-table-select pl-6 pr-4"
                  :value="everyItem"
                  :indeterminate="someItems && !everyItem"
                  @input="on['toggle-select-all']"
                />
              </div>
              <div v-else>
                <div
                  class="header-container"
                  :class="[
                    header.sortable ? 'sortable' : 'un-sortable',
                    sortDesc ? 'desc' : 'asc',
                    header.value === sortBy ? 'active' : 'de-active',
                    header.class,
                    getHeaderApplied(header.value) ? 'applied' : 'un-applied',
                  ]"
                >
                  {{ header.text }}
                  <v-icon class="icon-sort" small>$mdiArrowUpThin</v-icon>
                </div>
              </div>
            </th>
          </tr>
        </thead>
      </template>
      <template v-slot:item.code="{ item }">
        <div :class="`${item.textColor}TextTreeNode--text`">
          {{ item.code }}
        </div>
      </template>
      <template v-slot:item.firstMeasurementValue="{ item }">
        <div :class="`${item.textColor}TextTreeNode--text`">
          {{
            item.firstMeasurementValue === null
              ? "-"
              : item.firstMeasurementValue
          }}
        </div>
      </template>
      <template v-slot:item.lastMeasurementDate="{ item }">
        <div :class="`${item.textColor}TextTreeNode--text`">
          {{
            item.lastMeasurementDate === null
              ? "-"
              : new Date(item.lastMeasurementDate).toLocaleDateString()
          }}
        </div>
      </template>
      <template v-slot:item.lastMeasurementValue="{ item }">
        <div :class="`${item.textColor}TextTreeNode--text`">
          {{
            item.lastMeasurementValue === null ? "-" : item.lastMeasurementValue
          }}
        </div>
      </template>
      <template v-slot:item.previousMeasurementDeviation="{ item }">
        <div :class="`${item.textColor}TextTreeNode--text`">
          {{
            genStringValueNumberShock1AndPositive(
              item.previousMeasurementDeviation
            )
          }}
        </div>
      </template>
      <template v-slot:item.minLiftingHeight="{ item }">
        <div :class="`${item.textColor}TextTreeNode--text`">
          {{ item.minLiftingHeight === null ? "-" : item.minLiftingHeight }}
        </div>
      </template>
      <template v-slot:item.maxLiftingHeight="{ item }">
        <div :class="`${item.textColor}TextTreeNode--text`">
          {{ item.maxLiftingHeight === null ? "-" : item.maxLiftingHeight }}
        </div>
      </template>
      <template v-slot:item.firstMeasurementDeviation="{ item }">
        <div :class="`${item.textColor}TextTreeNode--text`">
          {{
            genStringFirstMeasurementDeviation(item.firstMeasurementDeviation)
          }}
        </div>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-tooltip bottom>
          <span>Открыть карточку объекта</span>
          <template v-slot:activator="{ on, attrs }">
            <NipiIconBtn v-on="on" v-bind="attrs" @click="openCardObject(item)">
              <v-icon size="16" v-text="'$mdiFileReplaceOutline'" />
            </NipiIconBtn>
          </template>
        </v-tooltip>
        <v-tooltip bottom>
          <span>Найти объект на карте</span>
          <template v-slot:activator="{ on, attrs }">
            <NipiIconBtn
              v-on="on"
              v-bind="attrs"
              @click="findObjectOnMap(item)"
            >
              <v-icon size="16" v-text="'$mdiMapSearchOutline'" />
            </NipiIconBtn>
          </template>
        </v-tooltip>
      </template>
    </v-data-table>
  </v-container>
</template>
<script>
import ActionsUtils from "@/assets/js/utils/actionsUtils";
import TableUsersFilter from "@/components/Cards/CardComponents/Tables/TableFilterParts/TableUsersFilter";
import TableCheckboxFilter from "@/components/Cards/CardComponents/Tables/TableFilterParts/TableСheckboxFilter";
import TableDateRangeFilter from "@/components/Cards/CardComponents/Tables/TableFilterParts/TableDateRangeFilter";
import { mapGetters, mapMutations } from "vuex";
import CategoryUtils from "@/assets/js/utils/categoryUtils";
import RoutesUtils from "@/assets/js/utils/routesUtils";
import CommonUtils from "@/assets/js/utils/commonUtils.js";

export default {
  props: {
    dmType: Boolean,
    items: {
      type: Array,
      required: true,
    },
    serverItemsLength: {
      type: Number,
      required: true,
    },
    itemsPerPage: {
      type: Number,
      required: true,
    },
    sortBy: {
      type: String,
      required: false,
    },
    sortDesc: {
      type: Boolean,
      required: true,
    },
    filterParams: {
      type: Object,
      required: true,
    },
    perPageItems: {
      type: Array,
      default() {
        return [10, 20, 40, 80];
      },
    },
    isLoading: {
      type: Boolean,
      default: false,
    },
    showSelect: {
      type: Boolean,
      default: false,
    },
    hideDeleteButton: {
      type: Boolean,
      default: false,
    },
  },

  data() {
    return {
      actionUtils: ActionsUtils,
      tableHeadersDm: [
        {
          text: "Код объекта",
          value: "code",
          sortable: true,
          width: "15%",
          class: "pl-6",
        },
        {
          text: "Значение первого измерения, м",
          value: "firstMeasurementValue",
          width: "10%",
          align: "center",
          sortable: true,
        },
        {
          text: "Дата последнего измерения",
          value: "lastMeasurementDate",
          width: "10%",
          align: "center",
          sortable: true,
        },
        {
          text: " Min высотное положение, м",
          value: "minLiftingHeight",
          width: "10%",
          align: "center",
          sortable: true,
        },
        {
          text: "Значение последнего измерения, м",
          value: "lastMeasurementValue",
          width: "10%",
          align: "center",
          sortable: true,
        },
         {
          text: "Max высотное положение, м",
          value: "maxLiftingHeight",
          width: "10%",
          align: "center",
          sortable: true,
        },
        {
          text: "Отклонение от первого измерения, мм",
          value: "firstMeasurementDeviation",
          width: "10%",
          align: "center",
          sortable: true,
        },
        {
          text: "Отклонение от предыдущего измерения, мм",
          value: "previousMeasurementDeviation",
          width: "10%",
          align: "center",
          sortable: true,
        },
        {
          text: "",
          value: "actions",
          width: "10%",
          align: "end",
          sortable: false,
        },
      ],
      selected: [],
      isCloseSearchMenuOnClick: false,
      tableHeadersOtherDm: [
        {
          text: "Код объекта",
          value: "code",
          sortable: true,
          width: "15%",
          class: "pl-6",
        },
        {
          text: "Значение первого измерения, м",
          value: "firstMeasurementValue",
          width: "10%",
          align: "center",
          sortable: true,
        },
        {
          text: "Дата последнего измерения",
          value: "lastMeasurementDate",
          width: "10%",
          align: "center",
          sortable: true,
        },
        {
          text: "Значение последнего измерения, м",
          value: "lastMeasurementValue",
          width: "10%",
          align: "center",
          sortable: true,
        },
        {
          text: "Отклонение от первого измерения, мм",
          value: "firstMeasurementDeviation",
          width: "10%",
          align: "center",
          sortable: true,
        },
        {
          text: "Отклонение от предыдущего измерения, мм",
          value: "previousMeasurementDeviation",
          width: "10%",
          align: "center",
          sortable: true,
        },
        {
          text: "",
          value: "actions",
          width: "10%",
          align: "end",
          sortable: false,
        },
      ],
    };
  },

  computed: {
    ...mapGetters(["selectedTreeNode"]),
  },

  methods: {
    ...mapMutations(["setSnack"]),


    /**
     * Преобразование числового значения в строку с обязотельным добавлением 1 разряда после запятой и знака '+' у положительного числа
     */
    genStringValueNumberShock1AndPositive(fValue) {
      let sValue = "-";

      if (typeof fValue == "number") {
        sValue = fValue.toFixed(1).toString();

        if (fValue > 0) {
          sValue = "+" + sValue;
        }
      }

      return sValue;
    },

    /**
     * Генерация строкового представления значения поля 'Отклонение от первого измерения' с преобразованием из метром в милиметры
     * и добавление знака "+" перед положительным числом
     */
    genStringFirstMeasurementDeviation(valueM) {
      let sValue = "-";

      if (typeof valueM == "number") {
        let value = CommonUtils.fromMToMM(valueM);

        sValue = this.genStringValueNumberShock1AndPositive(value);
      }

      return sValue;
    },

    findObjectOnMap(item) {
      this.$router.push(RoutesUtils.routes.MAP + `${item.id}`);
    },

    /**
     * Открытие карточкек разных элементов сети ГТМ
     *
     * @param item
     */
    openCardObject(item) {
      if (ActionsUtils.isOpenCard()) {
        this.$emit("openCardObject", item.objectId);
      }
    },

    /**
     * Открытие карточки объекта по двойному клику
     *
     * @param event - событие, при даблклик передается первым по умолчанию
     * @param rowTableData -  объект таблцы в поле item содержится строка по которой произведен клик
     */
    onDoubleClick(event, rowTableData) {
      if (ActionsUtils.isOpenCard()) {
        this.$emit("openCardObject", rowTableData.item.objectId);
      }
    },

    /**
     * Генерация события смены страницы
     */
    changePage(page) {
      this.$emit("changePage", page - 1);
    },

    /**
     * Генерация события смены количества элементов страницы
     */
    changeItemsPerPage(itemsPerPage) {
      this.$emit("changeItemsPerPage", itemsPerPage);
    },

    /**
     * Генерация события сортировки колонки
     */
    changeSortBy(column) {
      if (!column.sortable) return;
      if (this.sortBy === column.value) {
        this.$emit("changeSortDesc", !this.sortDesc);
      } else {
        this.$emit("changeSortBy", column.value);
        this.$emit("changeSortDesc", true);
      }
    },

    /**
     * Применить фильтр
     */
    applyFilter(name) {
      this.setHeaderApplied(name, true);
      this.isCloseSearchMenuOnClick = true;
      this.$emit("applyFilter", this.filterParams);
    },

    /**
     * Сбросить фильтр
     */
    cancelFilter(name) {
      this.getFilterComponent(name).cancelFilter();
      this.setHeaderApplied(name, false);
      this.isCloseSearchMenuOnClick = true;
      this.$emit("applyFilter", this.filterParams);
    },

    onSearchMenuOpen() {
      this.isCloseSearchMenuOnClick = false;
    },

    onSearchMenuClose() {
      this.isCloseSearchMenuOnClick = false;
    },

    getFilterItems(name) {
      return this.filterParams.paramsItems.find((item) => item.name == name)
        .items;
    },

    getFilterComponent(name) {
      return this.$refs.headerFilter.find(
        (item) => item._props.headerName == name
      );
    },

    getHeaderApplied(name) {
      if (name !== "data-table-select" && name !== "actions") {
        return this.filterParams.paramsItems.find((item) => item.name == name)
          .applied;
      }
    },

    setHeaderApplied(name, value) {
      this.filterParams.paramsItems.find((item) => item.name == name).applied =
        value;
    },

    /**
     * Генерация события сортировки колонки
     */
    changeSortDesc(sortDesc) {
      this.$emit("changeSortDesc", sortDesc);
    },

    /**
     * Снятие галочек с выбраных строк таблицы
     */
    deactivateSelection() {
      this.$refs.dataTable.selection = [];
    },

    /**
     * Получение списка выбраных строк таблицы
     */
    getSelection() {
      return Object.values(this.$refs.dataTable.selection);
    },
  },
};
</script>
<style scoped lang="scss">
.table-operation ::v-deep tr > td:first-child {
  padding-left: 24px;
}

.table-operation ::v-deep tr > th:first-child {
  padding-left: 24px;
}

.table-scroll {
  overflow-y: auto;
  height: calc(100vh - #{$header-height} - (#{$toolbar-height} * 3));
}

.row-cursor ::v-deep tr {
  cursor: pointer;
}

.table-operation ::v-deep .v-data-table__progress th {
  padding: 0 !important;
}

.header-container.sortable.active {
  color: var(--v-primary-base);
}

.header-container.un-sortable {
  cursor: default;
}

.header-container.sortable.asc.active > .icon-sort {
  transform: rotate(180deg);
  color: var(--v-primary-base);
  opacity: 1;
}

.header-container.sortable.desc.active > .icon-sort {
  transform: none;
  color: var(--v-primary-base);
  opacity: 1;
}

.header-container.sortable.de-active > .icon-sort {
  opacity: 0;
}

.header-container.sortable.de-active:hover {
  color: var(--v-primary-base);
}

.header-container.sortable.de-active:hover > .icon-sort {
  opacity: 1;
}

.header-container.un-sortable > .icon-sort {
  opacity: 0;
}

.header-container.applied ::v-deep .icon-filter {
  color: dodgerblue;
}

.item-table-select {
  margin: auto;
}
</style>
