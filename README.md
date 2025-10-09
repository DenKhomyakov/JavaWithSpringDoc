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
Фронтенд часть, SummaryTableDialog.vue:
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
            :hide-default-header="true"
            :hide-default-footer="true"
            disable-sort
            class="table-gtm-elements mr-10 ml-10"
            :footer-props="{
              itemsPerPageOptions: perPageItems,
              showCurrentPage: true,
              showFirstLastPage: true,
            }"
            @update:items-per-page="changeItemsPerPage"
            @update:page="changePage"
        >
          <!--Надо ли это?-->
          <template v-slot:header="{ props: { headers }, on }">
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
                    <span v-on="on"> {{ header.text }} </span>
                  </template>
                  <span>
                    {{ header.tooltip }}
                  </span>
                </v-tooltip>
                <div v-else>
                  {{ header.text }}
                </div>
              </th>
            </tr>
            </thead>
          </template>

          <!--Наименование параметра-->
          <template v-slot:item.parameterName="{ item }">
            <div :class="genClassColorForDiv(item)">
              {{
                item.parameterName === null
                    ? "-"
                    : item.parameterName
              }}
            </div>
          </template>

          <!--Месторождение-->
          <template v-slot:item.mine="{ item }">
            <div :class="genClassColorForDiv(item)">
              {{
                item.mine === null
                    ? "-"
                    : item.mine
              }}
            </div>
          </template>

          <!--Площадка-->
          <template v-slot:item.platform="{ item }">
            <div :class="genClassColorForDiv(item)">
              {{
                item.platform === null
                    ? "-"
                    : item.platform
              }}
            </div>
          </template>

          <!--Объект-->
          <template v-slot:item.object="{ item }">
            <div :class="genClassColorForDiv(item)">
              {{
                item.object === null
                    ? "-"
                    : item.object
              }}
            </div>
          </template>

          <!--Расчеты-->
          <template v-slot:item.calculations="{ item }">
            <div :class="genClassColorForDiv(item)">
              {{
                item.calculations === null
                    ? "-"
                    : item.calculations
              }}
            </div>
          </template>

          <!--ДМ-->
          <template v-slot:item.dm="{ item }">
            <div :class="genClassColorForDiv(item)">
              {{
                item.dm === null
                    ? "-"
                    : item.dm
              }}
            </div>
          </template>

          <!--ТС-->
          <template v-slot:item.ts="{ item }">
            <div :class="genClassColorForDiv(item)">
              {{
                item.ts === null
                    ? "-"
                    : item.ts
              }}
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
import scssVar from "@/styles/exportVarToJs.scss";
import {mapMutations} from "vuex";

export default {
  name: "SummaryTableDialog",
  components: {
    DialogHeader,
  },
  props: ["summaryTable"],
  data() {
    return {
      tableHeaders: [
        {
          text: "Наименование параметра",
          value: "parameterName",
          width: "15%",
          align: "center",
          class: "pl-6"
        },
        {
          text: "Месторождение",
          value: "mine",
          width: "15%",
          align: "center"
        },
        {
          text: "Площадка",
          value: "platform",
          width: "15%",
          align: "center"
        },
        {
          text: "Объект",
          value: "object",
          width: "15%",
          align: "center"
        },
        {
          text: "Расчеты",
          value: "calculations",
          width: "15%",
          align: "center"
        },
        {
          text: "ДМ",
          value: "dm",
          width: "15%",
          align: "center"
        },
        {
          text: "ТС",
          value: "ts",
          width: "15%",
          align: "center"
        },
      ],
      perPageItems: [10, 20, 40, 80],
      serverItemsLength: 10,
      itemsPerPage: 10,
      pageNumber: 0,
      deviationStructuresItems: [],
    };
  },

  mounted() {
    this.getDeviationStructuresItems();
  },

  // Что это вообще такое?
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
     * Генерация описание Класса для Div показывающего значение ячейки
     */
    // ToDo: Разобраться с тем, что это; взято из DeformationMarkReabilityIndicators.vue
    genClassColorForDiv(item) {
      return item.state
          ? "greenTextTreeNode--text"
          : "redTextTreeNode--text";
    },

    /**
     * Получение данных об отклонении структурных элементов (с Backend-части)
     */
    getDeviationStructuresItems() {

      // Контроллер SummaryTableController: /api/summaryTable
      // Метод getSummaryTable: {size}/{page}
      this.axios
          .get(`/summaryTable/itemsPerPage/pageNumber`)
          .then((response) => {
            this.deviationStructuresItems = response.data.data;
          })
          .catch((thrown) => {
            console.error(thrown.response);

            if (thrown.response && thrown.response.data.message()) {
              this.deviationStructuresItems.push({error: thrown.response.data.message});
            }
          })
    },

    /**
     * Обработка смены страницы
     */
    changePage(page) {
      this.pageNumber = page - 1;
    },

    /**
     * Обработка смены количества элементов страницы
     */
    changeItemsPerPage(itemsPerPage) {
      this.itemsPerPage = itemsPerPage;
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

// Для попытки расположить текст в середине блока <div>
.center-flexbox {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
Бэкенд часть:
/**
 * Строка сводной таблицы, соответствующая одной конструкции.
 */
@Getter
@Setter
@AllArgsConstructor
public class SummaryTableDto {
    /**
     * Название ЛУ
     */
    @JsonProperty("licenseAreaName")
    private String licenseAreaName;

    /**
     * Название площадки
     */
    @JsonProperty("projectName")
    private String projectName;

    /**
     * Название площадного объекта или пикета
     */
    @JsonProperty("constructionName")
    private String constructionName;

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
}

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

(Метод findAllByStateIdAndCategoryIdIn)
@Repository
public interface TreeNodeRepository extends JpaRepository<TreeNode, UUID>, CustomSortedTreeNodeRepository<TreeNode> {
    List<TreeNode> getAllByParentId(UUID parentId);

    List<TreeNode> findAllByParentIdAndCategoryIdNotIn(UUID parentId, Set<UUID> categoryIds);

    List<TreeNode> findAllByParentIdAndCategoryId(UUID parentId, UUID categoryId);

    List<TreeNode> findAllByIdIn(List<UUID> ids);

    List<TreeNode> findAllByObjectIdIn(List<UUID> objectIds);

    List<TreeNode> findAllByParentId(UUID parentId);
    List<TreeNode> findAllByParentIdIn(List<UUID> parentIds);

    Page<TreeNode> findAllByParentIdAndCategoryId(@Param("parentId") TypedParameterValue parentId,
                                                  @Param("categoryId") TypedParameterValue categoryId,
                                                  Pageable pageable);

    @Query("   select t" +
            "   from TreeNode t " +
            "   where (t.parent = :parentId and t.category = :categoryId)" +
            "       and ((exists (select 1 from Binding b where t.id = b.binded and lower(b.name) LIKE %:searchText%))" +
            "       or lower(t.name) LIKE %:searchText% or lower(t.code) LIKE %:searchText%)")
    Page<TreeNode> findAllByParentIdAndCategoryId(@Param("parentId") TypedParameterValue parentId,
                                                  @Param("categoryId") TypedParameterValue categoryId,
                                                  @Param("searchText") String searchText,
                                                  Pageable pageable);

    TreeNode getByObjectId(UUID objectId);

    Optional<TreeNode> findByObjectId(UUID objectId);

    TreeNode getByObjectIdOrderByCodeAsc(UUID objectId);

    @Query("select t from TreeNode t where cast(t.geometryObject.id as java.lang.String) in ?1")
    List<TreeNode> findAllByGeometryObjectIdIn(List<String> ids);

    @Query("select t from TreeNode t " +
            " where t.category.id = :buildingCategoryId or " +
            "t.category.id in (select distinct cc.childCategory.id from CategoryChildren cc where cc.isParentCategory = true )"
   )
    Page<TreeNode> findGtmTreeNodesAndBuilding(@Param("buildingCategoryId") UUID buildingCategoryId, Pageable pageable );

    @Query("select c.category.id from TreeNode c where c.objectId  = ?1")
    UUID findCategoryIdByObjectId(UUID id);

    TreeNode findFirstByCode(String code);

    @Query(value = "   select t.* " +
            "   from tree_node t " +
            "   where ((cast(:parentId as uuid) is null and t.parent_id is null) or t.parent_id = :parentId) and " +
            "   (exists ( " +
            "   with recursive name_tree as (  " +
            "      select id, parent_id, code, object_id, category_id " +
            "      from tree_node  " +
            "      where parent_id = t.id " +
            "      union all " +
            "      select c.id, c.parent_id, c.code, c.object_id, c.category_id " +
            "      from tree_node c  " +
            "        join name_tree p on c.parent_id = p.id " +
            "   ) " +
            "   select * " +
            "   from name_tree " +
            "   where category_id = :categoryId ) or t.category_id = :categoryId) ", nativeQuery = true)
    List<TreeNode> findAllByParentIdAndExistingCategoryInChildIsTrue(@Param("parentId") TypedParameterValue parentId, @Param("categoryId") UUID categoryId);

    @Query(value = "   select t.* " +
            "   from tree_node t " +
            "   where ((cast(:parentId as uuid) is null and t.parent_id is null) or t.parent_id = :parentId) and " +
            "   exists ( " +
            "   with recursive name_tree as (  " +
            "      select id, parent_id, code, object_id, category_id " +
            "      from tree_node  " +
            "      where parent_id = t.id " +
            "      union all " +
            "      select c.id, c.parent_id, c.code, c.object_id, c.category_id " +
            "      from tree_node c  " +
            "        join name_tree p on c.parent_id = p.id " +
            "   ) " +
            "   select * " +
            "   from name_tree " +
            "   where category_id = :categoryId ) ", nativeQuery = true)
    List<TreeNode> findAllByParentIdAndExistingCategoryInChildIsTrueOnlyGeneral(@Param("parentId") TypedParameterValue parentId, @Param("categoryId") UUID categoryId);

    @Query(value =
            "with recursive name_tree as ( " +
                    "   select id, parent_id, code, category_id" +
                    "   from tree_node " +
                    "   where object_id = :projectId " +
                    "   union all " +
                    "   select c.id, c.parent_id, c.code, c.category_id " +
                    "   from tree_node c " +
                    "     join name_tree p on c.parent_id = p.id  " +
                    ") " +
                    "select code " +
                    "from name_tree " +
                    "where category_id = :categoryId", nativeQuery = true)
    List<String> findObjectsUnderProject(@Param("projectId") UUID projectId, @Param("categoryId") UUID categoryId);

    List<TreeNode> findByNameAndCode(String name, String code);

    @Query(value =
            "with recursive name_tree as ( " +
                    "   select *" +
                    "   from tree_node " +
                    "   where object_id = :projectId " +
                    "   union all " +
                    "   select c.* " +
                    "   from tree_node c " +
                    "     join name_tree p on c.parent_id = p.id  " +
                    ") " +
                    "select * " +
                    "from name_tree " +
                    "where category_id in :categoryIds", nativeQuery = true)
    List<TreeNode> findObjectsUnderProjectByCategories(@Param("projectId") UUID projectId, @Param("categoryIds") List<UUID> categoryIds);

    @Query(value =
            "with recursive name_tree as ( " +
                    "   select *" +
                    "   from tree_node " +
                    "   where object_id = :objectId " +
                    "   union all " +
                    "   select c.* " +
                    "   from tree_node c " +
                    "     join name_tree p on c.parent_id = p.id  " +
                    ") " +
                    "select * " +
                    "from name_tree " +
                    "where category_id in :categoryIds", nativeQuery = true)
    List<TreeNode> findObjectsUnderParentObjectByCategories(@Param("objectId") UUID objectId, @Param("categoryIds") List<UUID> categoryIds);

    @Query(value = " with recursive temp1 as ( " +
            " select * from tree_node where object_id = :objectId " +
            " union all " +
            " select t2.* from tree_node t2 join temp1 t1 on (t1.parent_id = t2.id)) " +
            " select * from temp1; ", nativeQuery = true)
    List<TreeNode> findParentIdsByObjectId(@Param("objectId") UUID objectId);

    @Query(value = " with recursive temp1 as ( " +
            " select * from tree_node where id = :parentId " +
            " union all " +
            " select t2.* from tree_node t2 join temp1 t1 on (t1.parent_id = t2.id)) " +
            " select * from temp1; ", nativeQuery = true)
    List<TreeNode> findParentIdsByParentId(@Param("parentId") UUID parentId);

    @Query(value = " with recursive temp1 as ( " +
            " select * from tree_node where id = :treeNodeId " +
            " union all " +
            " select t2.* from tree_node t2 join temp1 t1 on (t1.parent_id = t2.id)) " +
            " select * from temp1; ", nativeQuery = true)
    List<TreeNode> findParentIdsByTreeNodeId(@Param("treeNodeId") UUID treeNodeId);

    List<TreeNode> findAllByCodeLikeOrNameLike(String codePart, String namePart);

    List<TreeNode> findByCodeLike(String codePart);

    List<TreeNode> findAllByCodeContainsIgnoreCaseOrNameContainsIgnoreCase(String codePart, String namePart);

    List<TreeNode> findAllByCategoryId(UUID categoryId);

    @Query("select t from TreeNode t" +
        "   where t.stateId = :stateId and t.category.id in :categoryIds")
    Page<TreeNode> findAllByStateIdAndCategoryIdIn(@Param("stateId") Integer stateId,
                                                   @Param("categoryIds") List<UUID> categoryIds,
                                                   Pageable pageable);

    List<TreeNode> findByParentId(UUID treeNodeId);

    List<TreeNode> findAllByParentIdAndCategoryIdAndCodeAndIdIsNot(UUID parentId, UUID categoryId, String code, UUID id);

    void deleteByIdIn(List<UUID> ids);
}

@Service
@AllArgsConstructor
public class SummaryTableService {

    private final TreeNodeRepository treeNodeRepository;
    private final ThermometricWellElementRepository thermometricWellElementRepository;
    private final DeformationMarkElementRepository deformationMarkElementRepository;

    private final ReliabilityIndicatorsService reliabilityIndicatorsService;

    final private List<UUID> constructionCategoryIds = Arrays.asList(
            Category.Constants.BUILDING_ID,
            Category.Constants.PICKETS_ID);

    /**
     * Получить страницу сводной таблицы.
     * Одна строка - одно сооружение с отклонениями. Если нет отклонений или не хватает данных для их подсчета, то
     * сооружение не попадает в таблицу.
     *
     * @param page  Номер страницы
     * @param size  Количество элементов на странице
     * @return      Страница сводной таблицы
     */
    public Page<SummaryTableDto> getSummaryTablePage(int page, int size) {
        Page<TreeNode> treeNodes = treeNodeRepository.findAllByStateIdAndCategoryIdIn(StateColorEnum.RED.colorCode,
                constructionCategoryIds,
                PageRequest.of(page, size));
        return new PageImpl<>(
                treeNodes.getContent().stream()
                        .map(tn -> new SummaryTableDto(findFirstParentByCategoryId(tn, Category.Constants.LICENSE_AREA_ID).getName(),
                                findFirstParentByCategoryId(tn, Category.Constants.PROJECT_ID).getName(),
                                tn.getName(),
                                getLastPileCalcStatus(tn),
                                getDeformationMarkStatus(tn),
                                getThermometricWellStatus(tn))).collect(Collectors.toList()),
                PageRequest.of(page, size), treeNodes.getTotalElements());
    }

    /**
     * Находит родительский узел искомой категории и выбирает среди них ближайшего родственника к указанному узлу.
     * Чем длиннее код, тем ближе к дочернему узлу находится родитель.
     *
     * @param treeNode      Дочерний узел
     * @param categoryId    Id категории искомого родителя
     * @return              Искомый родитель
     */
    private TreeNode findFirstParentByCategoryId(TreeNode treeNode, UUID categoryId) {
        return treeNodeRepository.findParentIdsByTreeNodeId(treeNode.getId()).stream()
                .filter(tn -> Category.isEquals(tn, categoryId))
                .max(Comparator.comparing(tn -> tn.getCode().length()))
                .orElse(null);
    }

    /**
     * Находит состояние последнего свайного расчета конструкции.
     *
     * @param treeNode  TreeNode конструкции
     * @return          true - все ок, false - есть отклонения, null - не удалось посчитать
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
     * Находит состояние по ДМ конструкции.
     *
     * @param treeNode  TreeNode конструкции
     * @return          true - все ок, false - есть отклонения, null - не удалось посчитать
     */
    private Boolean getDeformationMarkStatus(TreeNode treeNode) {
        TypedParameterValue tpvParentId = new TypedParameterValue(PostgresUUIDType.INSTANCE, treeNode.getId());
        List<DeformationMarkElement> dmList = deformationMarkElementRepository.findAllByParentId(tpvParentId);
        if (dmList.stream().allMatch(dm -> Objects.isNull(dm.getStateId()))) {
            return null;
        } else {
            return dmList.stream().noneMatch(dm -> StateColorEnum.RED.colorCode.equals(dm.getStateId()));
        }
    }

    /**
     * Находит состояние по ТС конструкции.
     *
     * @param treeNode  TreeNode конструкции
     * @return          true - все ок, false - есть отклонения, null - не удалось посчитать
     */
    private Boolean getThermometricWellStatus(TreeNode treeNode) {
        TypedParameterValue tpvParentId = new TypedParameterValue(PostgresUUIDType.INSTANCE, treeNode.getId());
        TypedParameterValue tpvCategoryId = new TypedParameterValue(PostgresUUIDType.INSTANCE, Category.Constants.TS_ID);
        List<ThermometricWellElement> tsList = thermometricWellElementRepository
                .findAllByParentIdAndCategoryId(tpvParentId, tpvCategoryId);
        if (tsList.stream().allMatch(dm -> Objects.isNull(dm.getStateId()))) {
            return null;
        } else {
            return tsList.stream().noneMatch(ts -> StateColorEnum.RED.colorCode.equals(ts.getStateId()));
        }
    }
}

Близкий пример DeformationMarkReabilityIndicators.vue:
<template>
  <v-container ma-0 pa-0 fluid>
    <v-toolbar
      flat
      :height="toolbarHeight"
      color="headerBackground"
      class="pl-2 pr-7"
    >
      <v-toolbar-title class="mr-auto header-font-color">
        Индикаторы надёжности
      </v-toolbar-title>
      <v-toolbar-items>
        <v-row>
          <div class="align-self-center">
            <v-tooltip bottom>
              <span> Обновить индикаторы надежности </span>
              <template v-slot:activator="{ on, attrs }">
                <NipiIconBtn
                  v-on="on"
                  v-bind="attrs"
                  color="headerFont"
                  class="mx-1"
                  @click="getReliabilityIndicators"
                >
                  <v-icon v-text="'$mdiDatabaseRefresh'"></v-icon>
                </NipiIconBtn>
              </template>
            </v-tooltip>
          </div>
        </v-row>
      </v-toolbar-items>
    </v-toolbar>

    <v-card-text slot="before-headers">
      <div :class="'redTextTreeNode--text'">
        <b color="error">
          {{
            checkLastMeasurementDate != null
              ? checkLastMeasurementDate.message
              : ""
          }}
        </b>
      </div>
    </v-card-text>

    <v-card-text slot="before-headers">
      <div>
        {{ safetyShortState.message }}
      </div>
    </v-card-text>

    <div>
      <v-data-table
        :headers="tableHeaders"
        :items="indicatorItems"
        :server-items-length="serverItemsLength"
        :items-per-page="itemsPerPage"
        :hide-default-header="true"
        :hide-default-footer="true"
        disable-sort
        class="table-gtm-elements"
        :footer-props="{
          itemsPerPageOptions: perPageItems,
          showCurrentPage: true,
          showFirstLastPage: true,
        }"
        @update:items-per-page="changeItemsPerPage"
        @update:page="changePage"
      >
        <template v-slot:header="{ props: { headers }, on }">
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
                    <span v-on="on"> {{ header.text }} </span>
                  </template>
                  <span>
                    {{ header.tooltip }}
                  </span>
                </v-tooltip>
                <div v-else>
                  {{ header.text }}
                </div>
              </th>
            </tr>
          </thead>
        </template>

        <template v-slot:item.code="{ item }">
          <div :class="genClassColorForDiv(item)">{{ item.code }}</div>
        </template>

        <template v-slot:item.differenceOfTotalDeflection="{ item }">
          <div :class="genClassColorForDiv(item)">
            {{
              item.differenceOfTotalDeflection === null
                ? "-"
                : item.differenceOfTotalDeflection
            }}
          </div>
        </template>

        <template v-slot:item.distance="{ item }">
          <div :class="genClassColorForDiv(item)">
            {{ item.distance === null ? "-" : item.distance }}
          </div>
        </template>

        <template v-slot:item.relativeDifferenceOfDraft="{ item }">
          <div :class="genClassColorForDiv(item)">
            {{
              item.relativeDifferenceOfDraft === null
                ? "-"
                : item.relativeDifferenceOfDraft
            }}
          </div>
        </template>

        <template v-slot:item.parentRelativeDifferenceOfDraft="{ item }">
          <div :class="genClassColorForDiv(item)">
            {{
              item.parentRelativeDifferenceOfDraft === null
                ? "-"
                : item.parentRelativeDifferenceOfDraft
            }}
          </div>
        </template>

        <template v-slot:item.lastMeasurementDate="{ item }">
          <div :class="genClassColorForDiv(item)">
            {{
              item.lastMeasurementDate === null
                ? "-"
                : new Date(item.lastMeasurementDate).toLocaleDateString()
            }}
          </div>
        </template>

        <template v-slot:item.error="{ item }">
          <div :class="genClassColorForDiv(item)">
            {{ item.error }}
          </div>
        </template>
      </v-data-table>
    </div>
  </v-container>
</template>
<script>
import scssVar from "@/styles/exportVarToJs.scss";
import { mapMutations } from "vuex";

export default {
  props: ["deformationMarkId"],
  data() {
    return {
      toolbarHeight: scssVar.toolbarHeight,
      tableHeaders: [
        {
          text: "Код классификатора",
          value: "code",
          width: "15%",
          align: "center",
          class: "pl-6",
        },
        {
          text: "ΔS, мм",
          value: "differenceOfTotalDeflection",
          width: "10%",
          align: "center",
          tooltip:
            "Разница общего отклонения по вертикали между текущей ДМ и соседними ДМ сооружения",
        },
        {
          text: "L, м",
          value: "distance",
          width: "10%",
          align: "center",
          tooltip: "Расстояние между текущей ДМ и соседней ДМ",
        },
        {
          text: "Факт",
          value: "relativeDifferenceOfDraft",
          width: "10%",
          align: "center",
          tooltip: "Фактическая относительная разность осадок",
        },
        {
          text: "Проект",
          value: "parentRelativeDifferenceOfDraft",
          width: "10%",
          align: "center",
          tooltip: "Проектная относительная разность осадок",
        },
        {
          text: "Дата последнего измерения",
          value: "lastMeasurementDate",
          width: "10%",
          align: "center",
        },
        {
          text: "Ошибки",
          value: "error",
        },
      ],
      perPageItems: [10, 20, 40, 80],
      serverItemsLength: 10,
      itemsPerPage: 10,
      pageNumber: 0,
      indicatorItems: [],
      checkLastMeasurementDate: null,
      safetyShortState: "",
    };
  },

  mounted() {
    this.getReliabilityIndicators();
  },

  methods: {
    ...mapMutations(["setSnack"]),

    /**
     * Генерация описание Класса для Div показывающего значение ячейки
     */
    genClassColorForDiv(item) {
      return item.state ? "greenTextTreeNode--text" : "redTextTreeNode--text";
    },

    /**
     * Получить индикаторы надежности ДМ
     */
    getReliabilityIndicators() {
      this.indicatorItems = [];

      this.axios
        .get(`/reliabilityIndicators/deformationMark/${this.deformationMarkId}`)
        .then((response) => {
          this.indicatorItems = response.data.data;
        })
        .catch((thrown) => {
          console.error(thrown.response);
          if (thrown.response && thrown.response.data.message) {
            this.indicatorItems.push({ error: thrown.response.data.message });
          }
        });

      this.getShortState();
      this.checkLastDateMeasure();
    },

    /**
     * Получить краткий статус объекта ДМ
     */
    getShortState() {
      this.axios
        .get(
          `/reliabilityIndicators/deformationMark/short/state/${this.deformationMarkId}`
        )
        .then((response) => {
          this.safetyShortState = response.data.data;
        })
        .catch((thrown) => {
          console.error(thrown.response);
          this.setSnack({
            message:
              thrown.response && thrown.response.data.message
                ? thrown.response.data.message
                : "Не удалось получить статус максимальных значений по вертикали",
            color: "error",
          });
        });
    },

    /**
     * Проверка дат последнего измерения объектов ДМ
     */
    checkLastDateMeasure() {
      this.axios
        .get(
          `/reliabilityIndicators/deformationMark/checkLastDateMeasures/${this.deformationMarkId}`
        )
        .then((response) => {
          this.checkLastMeasurementDate = response.data.data;
        })
        .catch((thrown) => {
          console.error(thrown.response);
          this.setSnack({
            message:
              thrown.response && thrown.response.data.message
                ? thrown.response.data.message
                : "Не удалось получить статус Даты последнего измерения",
            color: "error",
          });
        });
    },

    /**
     * Обработка смены страницы
     */
    changePage(page) {
      this.pageNumber = page - 1;
    },

    /**
     * Обработка смены количества элементов страницы
     */
    changeItemsPerPage(itemsPerPage) {
      this.itemsPerPage = itemsPerPage;
    },
  },
};
</script>

<style scoped>
.header-cell {
  text-align: center !important;
}
</style>
