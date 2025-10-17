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
Фронтенд, SummaryTableDialog.vue:
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

      const filterParams = [];

      if (this.selectedFilters.includes('calculations')) {
        filterParams.calculations = true;
      }

      if (this.selectedFilters.includes('dm')) {
        filterParams.deformationMarks = true;
      }

      if (this.selectedFilters.includes('ts')) {
        filterParams.thermometricWells = true;
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
    // applyFilters() {
    //   if (this.selectedFilters.length === 0) {
    //     this.filteredItems = [...this.deviationStructuresItems];
    //
    //     return;
    //   }
    //
    //   this.filteredItems = this.deviationStructuresItems.filter(item => {
    //     return this.selectedFilters.every(filter => {
    //       switch (filter) {
    //         case 'calculations':
    //           return item.lastPileCalcStatus !== null;
    //         case 'dm':
    //           return item.deformationMarkStatus !== null;
    //         case 'ts':
    //           return item.thermometricWellStatus !== null;
    //         default:
    //           return true;
    //       }
    //     });
    //   });
    // },

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
Бэкенд:
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
public class SummaryTableRow {

    /**
     * ID
     */
    @Id
    @GeneratedValue(generator = "uuid2")
    @GenericGenerator(name = "uuid", strategy = "uuid2")
    @Column(name = "id")
    private UUID id;

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
    private Boolean thermometricWellStatus;

    /**
     * Состояние по ДМ сооружения.
     * Вычисляется во вьюшке в базе по state_id дочерних ДМ сооружения.
     * Результат:
     * state_id дочерних ДМ отсутствуют -> значение null;
     * хотя бы один state_id = false -> принимает значение false;
     * иначе -> true.
     */
    @Column(name = "deformation_mark_state")
    private Boolean deformationMarkStatus;
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
        this.lastPileCalcStatus = lastPileCalcStatus;
        this.deformationMarkStatus = entity.getDeformationMarkStatus();
        this.thermometricWellStatus = entity.getThermometricWellStatus();
    }
}
@Repository
public interface SummaryTableRowRepository extends JpaRepository<SummaryTableRow, UUID>,
        JpaSpecificationExecutor<SummaryTableRow> {
}
@Log4j2
@RestController
@AllArgsConstructor
@RequestMapping("/api/summaryTable")
public class SummaryTableController extends BaseController {

    private final SummaryTableService summaryTableService;

    /**
     * Получить страницу сводной таблицы.
     * Одна строка - одно сооружение с отклонениями. Если нет отклонений или не хватает данных для их подсчета, то
     * сооружение не попадает в таблицу.
     *
     * @param page  Номер страницы
     * @param size  Количество элементов на странице
     * @return Страница сводной таблицы
     */
    @GetMapping("{size}/{page}")
    public ResponseEntity<?> getSummaryTable(@PathVariable @NotNull(message = "Не передан обязательный параметр номера страницы!") int page,
                                             @PathVariable @NotNull(message = "Не передан обязательный параметр количества элементов на страницы!") int size) {
        try {
            return ResponseEntity.ok(summaryTableService.getSummaryTablePage(page, size));
        } catch (Exception e) {
            log.error(e);
            return Error(e.getMessage());
        }
    }

//    /**
//     * Получить страницу сводной таблицы с сортировкой.
//     * Одна строка - одно сооружение с отклонениями. Если нет отклонений или не хватает данных для их подсчета, то
//     * сооружение не попадает в таблицу.
//     *
//     * @param page          Номер страницы (отсчет с нуля)
//     * @param size          Количество элементов на странице
//     * @param sortBy        Имя поля для сортировки
//     * @param sortDesc      Признак сортировки по убыванию
//     * @param filterParams  Быстрые фильтры
//     * @return Страница сводной таблицы с элементами, отсортированными с заданными параметрами
//     */
//    @GetMapping("{size}/{page}/{sortBy}/{sortDesc}")
//    public ResponseEntity<?> getSummaryTable(@PathVariable @NotNull(message = "Не передан обязательный параметр номера страницы!") int page,
//                                             @PathVariable @NotNull(message = "Не передан обязательный параметр количества элементов на страницы!") int size,
//                                             @PathVariable @NotNull(message = "Не передан обязательный параметр поля сортировки!") String sortBy,
//                                             @PathVariable @NotNull(message = "Не передан обязательный параметр направления сортировки!") boolean sortDesc,
//                                             @RequestParam(required = false) String filterParams) {
//        try {
//            return ResponseEntity.ok(summaryTableService.getSummaryTablePage(page, size, sortBy, sortDesc, filterParams));
//        } catch (Exception e) {
//            log.error(e);
//            return Error(e.getMessage());
//        }
//    }

    @GetMapping("{size}/{page}/{sortBy}/{sortDesc}")
    public ResponseEntity<?> getSummaryTable(@PathVariable @NotNull(message = "Не передан обязательный параметр номера страницы!") int page,
                                             @PathVariable @NotNull(message = "Не передан обязательный параметр количества элементов на страницы!") int size,
                                             @PathVariable @NotNull(message = "Не передан обязательный параметр поля сортировки!") String sortBy,
                                             @PathVariable @NotNull(message = "Не передан обязательный параметр направления сортировки!") boolean sortDesc,
                                             @RequestParam(required = false, value = SummaryTableService.lastPileCalcStatusFieldName) Boolean lastPileCalcStatus,
                                             @RequestParam(required = false, value = SummaryTableService.deformationMarkStatusFieldName) Boolean deformationMarkStatus,
                                             @RequestParam(required = false, value = SummaryTableService.thermometricWellStatusFieldName) Boolean thermometricWellStatus) {
        try {
//            Map<String, Object> filterParams = SummaryTableService.getFiltersMap(lastPileCalcStatus, deformationMarkStatus, thermometricWellStatus);
            return ResponseEntity.ok(summaryTableService.getSummaryTablePage(page, size, sortBy, sortDesc, lastPileCalcStatus, deformationMarkStatus, thermometricWellStatus));
        } catch (Exception e) {
            log.error(e);
            return Error(e.getMessage());
        }
    }
}
@Service
@AllArgsConstructor
public class SummaryTableService {

    private final TreeNodeRepository treeNodeRepository;
    private final SummaryTableRowRepository summaryTableRowRepository;

    private final ReliabilityIndicatorsService reliabilityIndicatorsService;

    /**
     * Имя поля "Состояние последнего свайного расчета", подходящее для сортировки, но не принадлежащее сущности в базе.
     */
    // todo свалидировать все поля и убедиться что соответствует в точности полям json-ки
    public static final String idFieldName = "nodeId";
    public static final String nameFieldName = "nodeName";
    public static final String objectIdFieldName = "objectId";
    public static final String categoryIdFieldName = "categoryId";
    public static final String projectNameFieldName = "projectName";
    public static final String licenseAreaNameFieldName = "licenseAreaName";
    public static final String lastPileCalcStatusFieldName = "lastPileCalcStatus";
    public static final String deformationMarkStatusFieldName = "deformationMarkStatus";
    public static final String thermometricWellStatusFieldName = "thermometricWellStatus";

    // todo свалидировать лист убедиться что соответствует в точности полям json-ки
    public static final List<Pair> pairs = new ArrayList<Pair>() {{
        add(new Pair(idFieldName,
                Comparator.comparing(SummaryTableRowDto::getId, Comparator.nullsFirst(Comparator.naturalOrder())),
                Comparator.comparing(SummaryTableRowDto::getId, Comparator.nullsFirst(Comparator.naturalOrder()))));
        add(new Pair(nameFieldName,
                Comparator.comparing(SummaryTableRowDto::getName, Comparator.nullsFirst(Comparator.naturalOrder())),
                Comparator.comparing(SummaryTableRowDto::getName, Comparator.nullsFirst(Comparator.naturalOrder()))));
        add(new Pair(objectIdFieldName,
                Comparator.comparing(SummaryTableRowDto::getObjectId, Comparator.nullsFirst(Comparator.naturalOrder())),
                Comparator.comparing(SummaryTableRowDto::getObjectId, Comparator.nullsFirst(Comparator.naturalOrder()))));
        add(new Pair(categoryIdFieldName,
                Comparator.comparing(SummaryTableRowDto::getCategoryId, Comparator.nullsFirst(Comparator.naturalOrder())),
                Comparator.comparing(SummaryTableRowDto::getCategoryId, Comparator.nullsFirst(Comparator.naturalOrder()))));
        add(new Pair(projectNameFieldName,
                Comparator.comparing(SummaryTableRowDto::getProjectName, Comparator.nullsFirst(Comparator.naturalOrder())),
                Comparator.comparing(SummaryTableRowDto::getProjectName, Comparator.nullsFirst(Comparator.naturalOrder()))));
        add(new Pair(licenseAreaNameFieldName,
                Comparator.comparing(SummaryTableRowDto::getLicenseAreaName, Comparator.nullsFirst(Comparator.naturalOrder())),
                Comparator.comparing(SummaryTableRowDto::getLicenseAreaName, Comparator.nullsFirst(Comparator.naturalOrder()))));
        add(new Pair(lastPileCalcStatusFieldName,
                Comparator.comparing(SummaryTableRowDto::getLastPileCalcStatus, Comparator.nullsFirst(Comparator.naturalOrder())),
                Comparator.comparing(SummaryTableRowDto::getLastPileCalcStatus, Comparator.nullsFirst(Comparator.naturalOrder()))));
        add(new Pair(deformationMarkStatusFieldName,
                Comparator.comparing(SummaryTableRowDto::getDeformationMarkStatus, Comparator.nullsFirst(Comparator.naturalOrder())),
                Comparator.comparing(SummaryTableRowDto::getDeformationMarkStatus, Comparator.nullsFirst(Comparator.naturalOrder()))));
        add(new Pair(thermometricWellStatusFieldName,
                Comparator.comparing(SummaryTableRowDto::getThermometricWellStatus, Comparator.nullsFirst(Comparator.naturalOrder())),
                Comparator.comparing(SummaryTableRowDto::getThermometricWellStatus, Comparator.nullsFirst(Comparator.naturalOrder()))));
    }};

    @Getter
    @Setter
    @AllArgsConstructor
    public static class Pair {
        private String fieldName;
        private Comparator<SummaryTableRowDto> descComparator;
        private Comparator<SummaryTableRowDto> ascComparator;

        public Comparator<SummaryTableRowDto> getComparatorByDirection(boolean sortDesc) {
            return sortDesc ? descComparator : ascComparator;
        }
    }

    /**
     * Мапа, которая сопоставляет json-поля (на фронте) с полями сущности (в базе) для сортировки.
     * Ключ - имя поля json, значение - имя поля сущности для сортировки.
     */
    private static final Map<String, String> fieldMapper;

    // Инициализация и валидация статических полей класса
    static {
        validateLastPileCalcStatusFieldName();
        fieldMapper = newFieldMapper();
        validateFieldMapper();
    }

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
        return new PageImpl<>(mapToDto(tableRows.getContent()),
                PageRequest.of(page, size),
                tableRows.getTotalElements());
    }

    /**
     * Получить страницу сводной таблицы.
     * Одна строка - одно сооружение с отклонениями. Если нет отклонений или не хватает данных для их подсчета, то
     * сооружение не попадает в таблицу.
     *
     * @param page      Номер страницы
     * @param size      Количество элементов на странице
     * @param sortBy    Поле для сортировки
     * @param sortDesc  Направление сортировки
     * @return Страница сводной таблицы, отсортированной по полю
     */
    public Page<SummaryTableRowDto> getSummaryTablePage(int page,
                                                        int size,
                                                        String sortBy,
                                                        boolean sortDesc,
                                                        Boolean lastPileCalcStatus,
                                                        Boolean deformationMarkStatus,
                                                        Boolean thermometricWellStatus) {
        if (Objects.nonNull(lastPileCalcStatus) || sortBy.equals(lastPileCalcStatusFieldName)) {
            List<SummaryTableRowDto> tableRows = mapToDto(summaryTableRowRepository.findAll());

            // фильтрация
            List<Predicate<SummaryTableRowDto>> allPredicates = new ArrayList<>();
            if (Objects.nonNull(lastPileCalcStatus)) {
                allPredicates.add(dto -> Objects.equals(dto.getLastPileCalcStatus(), lastPileCalcStatus));
            }
            if (Objects.nonNull(deformationMarkStatus)) {
                allPredicates.add(dto -> Objects.equals(dto.getDeformationMarkStatus(), deformationMarkStatus));
            }
            if (Objects.nonNull(thermometricWellStatus)) {
                allPredicates.add(dto -> Objects.equals(dto.getThermometricWellStatus(), thermometricWellStatus));
            }
            Predicate<SummaryTableRowDto> predicate = allPredicates.stream()
                    .reduce(x -> true, Predicate::and);

            Comparator<SummaryTableRowDto> comparator = pairs.stream()
                    .filter(p -> p.getFieldName().equals(sortBy))
                    .findFirst()
                    .orElseThrow(() -> new BusinessException(""))
                    .getComparatorByDirection(sortDesc);

            tableRows = tableRows.stream()
                    .filter(predicate)
                    .sorted(comparator)
                    .collect(Collectors.toList());

            Pageable pageable = PageRequest.of(page, size);
            int start = (int) pageable.getOffset();
            int end = Math.min(start + pageable.getPageSize(), tableRows.size());
            return new PageImpl<>(tableRows.subList(start, end), pageable, tableRows.size());
        } else {
            String mappedSortBy = mapSortingField(sortBy);
            Sort.Direction direction = sortDesc ? Sort.Direction.DESC : Sort.Direction.ASC;
            Specification<SummaryTableRow> spec = createSpecification(deformationMarkStatus, thermometricWellStatus);

            Page<SummaryTableRow> tableRows = summaryTableRowRepository.findAll(spec, PageRequest.of(page, size, Sort.by(direction, mappedSortBy)));
            return new PageImpl<>(mapToDto(tableRows.getContent()),
                    PageRequest.of(page, size),
                    tableRows.getTotalElements());
        }
    }

    /**
     * Создает спецификацию для фильтрации
     */
    private Specification<SummaryTableRow> createSpecification(Boolean deformationMarkStatus,
                                                               Boolean thermometricWellStatus) {

        return (root, query, criteriaBuilder) -> {
            List<javax.persistence.criteria.Predicate> predicates = new ArrayList<>();

            // Фильтр по deformationMarkStatus
            if (Objects.nonNull(deformationMarkStatus)) {
                predicates.add(criteriaBuilder.equal(root.get("deformationMarkStatus"), deformationMarkStatus));
//                try {
//                    predicates.add(criteriaBuilder.equal(root.get("deformationMarkState"), deformationMarkStatus));
//                } catch (IllegalArgumentException e) {
//                    // Поле не существует в базе
//                }
            }

            // Фильтр по thermometricWellStatus
            if (Objects.nonNull(thermometricWellStatus)) {
                predicates.add(criteriaBuilder.equal(root.get("thermometricWellStatus"), thermometricWellStatus));
//                try {
//                    predicates.add(criteriaBuilder.equal(root.get("thermometricWellState"), thermometricWellStatus));
//                } catch (IllegalArgumentException e) {
//                    // Поле не существует в базе
//                }
            }

            return predicates.isEmpty() ? null : criteriaBuilder.and(predicates.toArray(new javax.persistence.criteria.Predicate[0]));
        };
    }


    public List<SummaryTableRowDto> mapToDto(List<SummaryTableRow> entities) {
        return entities.stream()
                .map(row -> {
                    TreeNode treeNode = treeNodeRepository.findById(row.getId())
                            .orElseThrow(() -> new BusinessException("Не удалось найти treeNode по id: " + row.getId()));
                    return new SummaryTableRowDto(row, treeNode, getLastPileCalcStatus(treeNode));
                }).collect(Collectors.toList());
    }

    /**
     * Сопоставляет поле для сортировки из json с полем сущности.
     *
     * @param sortBy    Наименование поля json
     * @return наименование поля сущности
     */
    public String mapSortingField(String sortBy) {
        if (fieldMapper.containsKey(sortBy)) {
            return fieldMapper.get(sortBy);
        } else {
            throw new BusinessException(String.format("Не удалось применить сортировку по полю '%s'.", sortBy));
        }
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

    /**
     * Проверяет соответствие поля lastPileCalcStatusFieldName полю в дто, чтобы гарантировать корректность сортировки.
     */
    private static void validateLastPileCalcStatusFieldName() {
        try {
            SummaryTableRowDto.class.getDeclaredField(lastPileCalcStatusFieldName);
        } catch (NoSuchFieldException e) {
            throw new BusinessException(String.format("Не удалось найти поле %s в классе %s. Проверьте наименование поля.",
                    lastPileCalcStatusFieldName, SummaryTableRowDto.class.getSimpleName()));
        }
    }

    /**
     * Подготавливает мапу, которая сопоставляет json-поля (на фронте) с полями сущности (в базе) для сортировки.
     *
     * @return заполненный fieldMapper
     */
    private static Map<String, String> newFieldMapper() {
        final List<String> summaryTableRowFieldNames = Arrays.stream(SummaryTableRow.class.getDeclaredFields())
                .map(Field::getName)
                .collect(Collectors.toList());
        return Arrays.stream(SummaryTableRowDto.class.getDeclaredFields())
                .filter(f -> summaryTableRowFieldNames.contains(f.getName()) || lastPileCalcStatusFieldName.equals(f.getName()))
                .collect(Collectors.toMap(
                        f -> {
                            JsonProperty jsonProperty = f.getAnnotation(JsonProperty.class);
                            return Strings.isNullOrEmpty(jsonProperty.value()) ? f.getName() : jsonProperty.value();
                        },
                        f -> f.getName())
                );
    }

    /**
     * Проверяет, что имена полей сущности существуют и соответствуют именам полей в мапе.
     */
    private static void validateFieldMapper() {
        final List<String> summaryTableRowFieldNames = Arrays.stream(SummaryTableRow.class.getDeclaredFields())
                .map(Field::getName)
                .collect(Collectors.toList());
        summaryTableRowFieldNames.forEach(f -> {
            if (!fieldMapper.containsValue(f)) {
                throw new BusinessException(
                        String.format("Не удалось сопоставить поле %s класса %s с полем класса %s." +
                                        " Наименования общих полей в этих классах должны в точности соответствовать друг другу",
                                f, SummaryTableRow.class.getSimpleName(), SummaryTableRowDto.class.getSimpleName()));
            }
        });
    }
}


URL: http://localhost:8090/api/summaryTable/10/0/lastPileCalcStatus/false?deformationMarkStatus=false&lastPileCalcStatus=false&thermometricWellStatus=false
