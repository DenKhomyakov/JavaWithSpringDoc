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
Термометрическая скважина (ТС), фронтенд:
<template>
  <v-container ma-0 pa-0 fluid>
    <v-form ref="form" v-model="valid" flat class="pa-8" lazy-validation>
      <v-row class="pb-3">
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <v-tooltip bottom>
            <span>Наименование</span>
            <template v-slot:activator="{ on, attrs }">
              <div v-on="on" v-bind="attrs">
                <NipiTextField
                  label="Наименование"
                  v-model="treeNode.name"
                  :disabled="disabled"
                  maxlength="255"
                  :rules="[rules.required()]"
                />
              </div>
            </template>
          </v-tooltip>
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <v-tooltip top>
            <span> Дата забоя </span>
            <template v-slot:activator="{ on, attrs }">
              <div v-on="on" v-bind="attrs">
                <NipiDateInput
                  label="Дата забоя"
                  :value="thermometricWellProperties.slaughterDate"
                  @onChange="dateChange"
                  :disabled="disabled"
                />
              </div>
            </template>
          </v-tooltip>
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <v-tooltip bottom>
            <span> Проектное значение температуры грунта </span>
            <template v-slot:activator="{ on, attrs }">
              <div v-on="on" v-bind="attrs">
                <NipiTextField
                  label="Проектное значение температуры грунта"
                  :hint="`Число в диапазоне [-\u221E;+\u221E]`"
                  v-model="
                    thermometricWellProperties.groundTemperatureDesignValue
                  "
                  :disabled="disabled"
                  :rules="[rules.numberDecimal(3)]"
                />
              </div>
            </template>
          </v-tooltip>
        </v-col>
        <v-col
          class="py-2"
          :md="md"
          :xs="xs"
          :sm="sm"
          :lg="lg"
          :xl="xl"
          v-if="!isGtmNetwork"
        >
          <NipiSelect
            showTooltipLabel
            label="Группа свай"
            :disabled="disabledSelected"
            v-model="thermometricWellProperties.dictPileGroup"
            :items="valuesSelect.listPileGroup"
          />
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <v-tooltip top>
            <span> Конструктивные особенности </span>
            <template v-slot:activator="{ on, attrs }">
              <div v-on="on" v-bind="attrs">
                <NipiTextField
                  label="Конструктивные особенности"
                  :value="thermometricWellProperties.constructionFeatures"
                  :disabled="disabled"
                />
              </div>
            </template>
          </v-tooltip>
        </v-col>
        <v-col class="py-2" :md="12" :xs="12" :sm="12" :lg="12" :xl="12">
          <v-tooltip bottom>
            <span> Комментарий </span>
            <template v-slot:activator="{ on, attrs }">
              <div v-on="on" v-bind="attrs">
                <NipiTextarea
                  label="Комментарий"
                  v-model="thermometricWellProperties.comment"
                  :disabled="disabled"
                  maxlength="255"
                />
              </div>
            </template>
          </v-tooltip>
        </v-col>
      </v-row>

      <NipiCategoriesDivider
        class="my-8 divider-container"
        :md="md"
        :xs="xs"
        :sm="sm"
        :lg="lg"
        :xl="xl"
        :categoryName="'Сведения о последнем техническом осмотре'"
      />
      <v-row class="pb-3">
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <v-tooltip bottom>
            <span> Дата последнего технического осмотра </span>
            <template v-slot:activator="{ on, attrs }">
              <div v-on="on" v-bind="attrs">
                <NipiDateInput
                  label="Дата последнего технического осмотра"
                  :value="
                    thermometricWellProperties.visualInspection.dateInspection
                  "
                  :disabled="true"
                  hideIcon
                />
              </div>
            </template>
          </v-tooltip>
        </v-col>

        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <v-tooltip bottom>
            <span> Значение последнего технического осмотра </span>
            <template v-slot:activator="{ on, attrs }">
              <div v-on="on" v-bind="attrs">
                <NipiTextField
                  :value="
                    thermometricWellProperties.visualInspection &&
                    thermometricWellProperties.visualInspection.gtmElementState
                      ? thermometricWellProperties.visualInspection
                          .gtmElementState.name
                      : ''
                  "
                  :disabled="true"
                  label="Значение последнего технического осмотра"
                >
                </NipiTextField>
              </div>
            </template>
          </v-tooltip>
        </v-col>
      </v-row>
    </v-form>
  </v-container>
</template>
<script>
import validationRules from "@/assets/js/utils/validationRulesUtils";
import CategoryUtils from "@/assets/js/utils/categoryUtils";
import { mapGetters } from "vuex";
import { disable } from "ol/rotationconstraint";
import NipiTextField from "../../NipiComponents/NipiTextField.vue";

export default {
  components: {NipiTextField},
  props: {
    // Отключен ли режим редактирования, по умолчанию отключен
    disabled: {
      type: Boolean,
      default: true,
    },
    // Данные для заполнения полей, в data должно быть 2 поля treeNode и properties,
    // в которых хранятся объекта, структура этих объектов в блоке data этого компонента
    data: {
      type: Object,
      default: null,
    },
    categoryId: {
      type: String,
    },
  },

  data() {
    return {
      md: "3",
      xs: "12",
      sm: "6",
      lg: "4",
      xl: "3",
      valid: true,
      disabledSelected: this.disabled,
      thermometricWellProperties: {
        zeroPoint: null,
        slaughterDate: null,
        groundTemperatureDesignValue: null,
        constructionFeatures: null,
        comment: null,
        dictPileGroup: {
          id: null,
          pileGroupName: null,
        },
        visualInspection: {
          dateInspection: null,
          gtmElementState: { id: null, name: null },
        },
      },
      valuesSelect: {
        listPileGroup: [],
      },
      pileGroup: null,
      treeNode: { name: null },
      rules: validationRules,
      categories: CategoryUtils,
    };
  },

  computed: {
    ...mapGetters(["selectedTreeNode"]),
    isGtmNetwork() {
      if (!this.selectedTreeNode || !this.selectedTreeNode.parent) return false;
      return (
        this.selectedTreeNode.parent.categoryId ===
        "f58aceb5-bd4c-4237-7c2b-17b47d86a0cb"
      );
    },
  },
  mounted() {
    if (this.data) {
      this.thermometricWellProperties = this.data.properties;
      this.treeNode = this.data.treeNode;
      this.addInitialValueSelect(this.data.properties);
    }
  },

  watch: {
    disabled(val) {
      if (val) {
        this.disabledSelected = val;
      } else {
        this.getValueForSelected();
      }
    },
    data(val) {
      this.thermometricWellProperties = val.properties;
      this.treeNode = val.treeNode;
      this.addInitialValueSelect(val.properties);
    },
  },

  methods: {
    getValueForSelected() {
      this.axios
        .get(`/building/values/selected/${this.selectedTreeNode.parent.id}`)
        .then((response) => {
          this.valuesSelect.listPileGroup = response.data.data;
          if (this.valuesSelect.listPileGroup.length > 0) {
            this.valuesSelect.listPileGroup.forEach((item) => {
              item.name = item.name;
            });
          }
          this.disabledSelected = false;
        })
        .catch((thrown) => {
          //отключаем индикацию загрузки
          console.error(thrown.message);
          this.setSnack({
            message: "Не удалось получить группу свай у ТС",
            color: "error",
          });
          this.disabledSelected = true;
        });
    },
    /**
     * Проверка валидности полей
     * @returns {boolean} возвращает валидные или нет значения полей формы
     */
    validate() {
      return this.$refs.form.validate();
    },

    addInitialValueSelect(properties) {
      this.valuesSelect.listPileGroup.push(properties.dictPileGroup);
    },

    /**
     * Сброс значения всех полей
     */
    reset() {
      this.$refs.form.reset();
    },

    /**
     * Обратка изменения даты
     */
    dateChange(date) {
      this.thermometricWellProperties.slaughterDate = date;
    },
  },
};
</script>
Термометрическая скважина (ТС), бэкенд:
/**
 * Термометрическая скважина (ТС)
 */
@Entity
@Table(name = "thermometric_well")
@Getter
@Setter
@NoArgsConstructor
public class ThermometricWell extends BaseId implements IHasMeasurements {
    /**
     * Нулевая точка (от уровня моря), м
     */
    @Column(name = "zero_point")
    private Double zeroPoint = 0.0;

    /**
     * Дата забоя
     */
    @Column(name = "slaughter_date")
    private Timestamp slaughterDate;

    /**
     * Проектное значение температуры грунта
     */
    @Column(name = "ground_temperature_design_value")
    private Double groundTemperatureDesignValue;

    /**
     * Комментарий
     */
    @Column(name = "comment")
    private String comment;

    /**
     * Измерения ТС
     */
    @OneToMany(mappedBy = "thermometricWell"
            , cascade = CascadeType.ALL
            , orphanRemoval = true
            , fetch = FetchType.LAZY)
    private List<ThermometricWellMeasurement> measurements;

    @ManyToOne
    @JoinColumn(name = "pile_group_id", referencedColumnName = "id")
    private PileGroup pileGroup;

    /**
     * Конструктивные особенности ТС
     * */
    @Column(name = "construction_features")
    private String constructionFeatures;

    public ThermometricWell(ThermometricWell original) {
        this.setZeroPoint(original.getZeroPoint());
        this.setSlaughterDate(original.getSlaughterDate());
        this.setGroundTemperatureDesignValue(original.getGroundTemperatureDesignValue());
        this.setComment(original.getComment());
        this.setPileGroup(original.getPileGroup());
        this.setConstructionFeatures(original.getConstructionFeatures());
    }

    /**
     * Проверяет количество измерений у TC.
     *
     * @param count Минимально необходимое количество измерений.
     * @return Да - в случае, если количество измерений больше либо равно необходимому,
     * в противном случае - нет.
     */
    @Override
    public boolean hasCountOfMeasurements(int count) {
        return this.getMeasurements().size() >= count;
    }

    /**
     * Формирует отсортированную по дате проверки коллекцию значений температур на заданной глубине.
     * Если искомой глубины в измерении нет или температура на этой глубине не задана -
     * такой результат в результирующую коллекцию не попадает.
     * @param depth Заданная глубина.
     * @return Коллекция температур.
     */
    public List<Double> getTemperaturesAtDepthByVerificationDate(double depth) {
        return measurements.stream()
                .sorted(Comparator.comparing(BaseMeasurement::getVerificationDate))
                .map(m -> m.getTemperatureAtDepth(depth))
                .filter(Objects::nonNull)
                .collect(Collectors.toList());
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (o == null || getClass() != o.getClass()) {
            return false;
        }
        ThermometricWell tw = (ThermometricWell) o;
        return Objects.equals(this.getId(), tw.getId());
    }

    @Override
    public int hashCode() {
        return Objects.hash(this.getId());
    }
}
/**
 * Термометрическая скважина (ТС)
 */
@Getter
@NoArgsConstructor
public class ThermometricWellPropertiesDto {
    /**
     * Идентификатор объекта
     */
    @JsonProperty("id")
    private UUID id;

    /**
     * Нулевая точка (от уровня моря), м
     */
    @JsonProperty("zeroPoint")
    @DescriptionField("Нулевая точка (от уровня моря), м")
    @NumberDecimal(decimal = 3, message = "Количество знаков после запятой не должно превышать 3")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double zeroPoint;

    /**
     * Дата забоя
     */
    @JsonProperty("slaughterDate")
    @DescriptionField("Дата забоя")
    @BeforeDate(date = "31-12-2100", message = "Дата должна быть раньше 31-12-2100")
    @AfterDate(date = "01-01-1900", message = "Дата должна быть позже 01-01-1900")
    private LocalDate slaughterDate;

    /**
     * Проектное значение температуры грунта
     */
    @JsonProperty("groundTemperatureDesignValue")
    @DescriptionField("Проектное значение температуры грунта")
    @NumberDecimal(decimal = 3, message = "Количество знаков после запятой не должно превышать 3")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double groundTemperatureDesignValue;

    /**
     * Комментарий
     */
    @JsonProperty("comment")
    @DescriptionField("Комментарий")
    @Size(max = 255)
    private String comment;

    /**
     * Группа свай
     */
    @JsonProperty("dictPileGroup")
    @DescriptionField("Группа свай")
    private DictPileGroup dictPileGroup;

    /**
     * Последний технический осмотр
     */
    @JsonProperty("visualInspection")
    private GtsVisualInspectionDto visualInspection;

    /**
     * Конструктивные особенности ТС
     * */
    @JsonProperty("constructionFeatures")
    @DescriptionField("Конструктивные особенности ТС")
    @Size(max = 1000, message = "Количество символов не должно превышать 1000")
    private String constructionFeatures;

    public ThermometricWellPropertiesDto(ThermometricWell ts, GtsVisualInspectionDto visualInspection) {
        this.id = ts.getId();
        this.zeroPoint = ts.getZeroPoint();
        this.slaughterDate = ts.getSlaughterDate() != null ?
                ts.getSlaughterDate().toLocalDateTime().toLocalDate() : null;
        this.groundTemperatureDesignValue = ts.getGroundTemperatureDesignValue();
        this.comment = ts.getComment();
        if (ts.getPileGroup() != null) {
            this.dictPileGroup = new DictPileGroup(ts.getPileGroup());
        }
        this.visualInspection = visualInspection;
        this.constructionFeatures = ts.getConstructionFeatures();
    }
}
public class ThermometricWellCreatorHandler extends BaseCreatorHandler {
    private final Class<ThermometricWell> entityClass = ThermometricWell.class;
    private final Class<ThermometricWellMeasurement> measurementClass = ThermometricWellMeasurement.class;
    private final Class<ThermometricWellOperation> operationClass = ThermometricWellOperation.class;
    private final Class<GtsVisualInspection> gtsVisualInspection = GtsVisualInspection.class;
    private final Class<AddObjectDto> saveDtoClass = AddObjectDto.class;
    private final Class<AddMultipleObjectsDto> saveAddMultipleObjectsDtoClass = AddMultipleObjectsDto.class;
    private final Class<CopyObjectDto> copyDtoClass = CopyObjectDto.class;

    public ThermometricWellCreatorHandler(Repositories repositories, TransactionalHandler transactionalHandler, AppServiceForObjectCreatorChain appServiceForObjectCreatorChain) {
        super(repositories, transactionalHandler, appServiceForObjectCreatorChain);
    }

    @Override
    protected boolean isCategoryOfObject(UUID categoryId) {
        return categoryId.equals(Category.Constants.TS_ID);
    }

    @Override
    protected TreeNode save(Object dto) {
        return transactionalHandler.runInTransaction(() -> {
            AddObjectDto objectDto = castDto(dto, saveDtoClass);
            checkCodeDuplication(objectDto.getCode());
            ThermometricWellRepository chainRepository = (ThermometricWellRepository) repositories.getRepositoryFor(entityClass).get();
            ThermometricWell object = chainRepository.save(new ThermometricWell());

            return treeNodeRepository.save(
                    new TreeNode(objectDto.getCode(),
                            objectDto.getName(),
                            treeNodeRepository.getOne(objectDto.getParentId()),
                            null,
                            categoryRepository.getOne(objectDto.getCategoryId()),
                            object.getId())
            );
        });
    }

    @Override
    protected TreeNode copy(Object dto) {
        return transactionalHandler.runInTransaction(() -> {
            CopyObjectDto objectDto = castDto(dto, copyDtoClass);
            checkCodeDuplication(objectDto.getCode());
            ThermometricWellRepository chainRepository = (ThermometricWellRepository) repositories.getRepositoryFor(entityClass).get();
            ThermometricWell original = chainRepository.findById(objectDto.getCopiedId()).orElseThrow(
                    () -> new BusinessException("Не найден копируемый объект"));
            ThermometricWell object = chainRepository.save(new ThermometricWell(original));

            return treeNodeRepository.save(
                    new TreeNode(objectDto.getCode(),
                            objectDto.getName(),
                            treeNodeRepository.getOne(objectDto.getParentId()),
                            null,
                            categoryRepository.getOne(objectDto.getCategoryId()),
                            object.getId())
            );
        });
    }

    @Override
    protected List<TreeNode> multipleSave(AddMultipleObjectsDto dto) {
        return transactionalHandler.runInTransaction(() -> {
            ThermometricWellRepository chainRepository =
                    (ThermometricWellRepository) repositories.getRepositoryFor(entityClass).get();

            List<ThermometricWell> thermometricWellForSave = new ArrayList<>();
            for (int i = 0; i < dto.getCount(); i++) {
                thermometricWellForSave.add(new ThermometricWell());
            }

            List<ThermometricWell> savedObjects = chainRepository.saveAll(thermometricWellForSave);

            Long max = ObjectTreeHelper.getCountObjectsUnderTreeNode(treeNodeRepository,
                    dto.getCategoryId(),
                    dto.getParentId());
            TreeNode parentTreeNode = treeNodeRepository.getOne(dto.getParentId());
            Category category = categoryRepository.getOne(dto.getCategoryId());

            return multiSaveTreeNodes(max, category, savedObjects, parentTreeNode);
        });
    }

    @Override
    protected Object update(Object dto) {
        return transactionalHandler.runInTransaction(() -> {
            ThermometricWellCardDto thermometricWellCardDto = castDto(dto, ThermometricWellCardDto.class);

            ThermometricWellPropertiesDto propertiesDto = thermometricWellCardDto.getProperties();
            TreeNodeDto treeNodeDto = thermometricWellCardDto.getTreeNode();

            ThermometricWellRepository chainRepository = (ThermometricWellRepository) repositories.getRepositoryFor(entityClass).get();
            PileGroupRepository pileGroupRepository = (PileGroupRepository) repositories.getRepositoryFor(PileGroup.class).get();

            ThermometricWell thermometricWell = chainRepository.findById(propertiesDto.getId())
                    .orElseThrow(() -> new BusinessException("Не удалось найти объект"));

            thermometricWell.setSlaughterDate(propertiesDto.getSlaughterDate() != null ?
                    Timestamp.valueOf(propertiesDto.getSlaughterDate().atStartOfDay()) : null);
            thermometricWell.setGroundTemperatureDesignValue(propertiesDto.getGroundTemperatureDesignValue());
            thermometricWell.setComment(propertiesDto.getComment());

            thermometricWell.setPileGroup(propertiesDto.getDictPileGroup() != null
                    ? pileGroupRepository.getOne(propertiesDto.getDictPileGroup().getId()) : null);

            thermometricWell.setConstructionFeatures(propertiesDto.getConstructionFeatures());

            TreeNode treeNode = treeNodeRepository.findById(treeNodeDto.getId())
                    .orElseThrow(() -> new BusinessException("Не удалось найти узел дерева объектов"));
            treeNode.setName(treeNodeDto.getName());

            TreeNode savedTreeNode = treeNodeRepository.save(treeNode);
            ThermometricWell savedThermometricWell = chainRepository.save(thermometricWell);
            GtsVisualInspectionDto gtsVisualInspectionDto =
                    this.appServiceForObjectCreatorChain.getGtsVisualInspectionService().getLastVisualInspection(thermometricWellCardDto.getTreeNode().getObjectId());

            return new ThermometricWellCardDto(new TreeNodeDto(savedTreeNode),
                    new ThermometricWellPropertiesDto(savedThermometricWell, gtsVisualInspectionDto));
        });
    }

    @Override
    protected TreeNodeDto delete(UUID objectId) {
        return transactionalHandler.runInTransaction(() -> {
            ThermometricWellRepository chainRepository = (ThermometricWellRepository) repositories.getRepositoryFor(entityClass).get();
            chainRepository.delete(chainRepository.getOne(objectId));
            TreeNode treeNode = treeNodeRepository.getByObjectId(objectId);
            bindingRepository.deleteAllByBinded(treeNode.getId());
            treeNodeRepository.delete(treeNodeRepository.getByObjectId(objectId));
            return new TreeNodeDto(treeNode);
        });
    }

    @Override
    protected ImportableDto importCard(ImportableDto dto, OperationBlank blank) {
        return transactionalHandler.runInTransaction(() -> {
            ThermometricWellRepository chainRepository = (ThermometricWellRepository) repositories.getRepositoryFor(entityClass).get();
            List<OperationBlankField> blankFields = blank.getFields();

            for (OperationDto operation : dto.getOperations()) {
                ThermometricWell obj = chainRepository.getOne(operation.getObject().getObjectId());

                fillObjectPropertiesFromOperationBlankFields(operation,
                        blankFields,
                        chainRepository,
                        obj);

                chainRepository.save(obj);
            }

            return null;
        });
    }

    @Override
    protected ImportableDto importMeasurement(ImportableDto dto, OperationBlank blank, String userSid) {
        return transactionalHandler.runInTransaction(() -> {
            ThermometricWellRepository chainRepository = (ThermometricWellRepository) repositories.getRepositoryFor(entityClass).get();
            StorageRepository storageRepository = (StorageRepository) repositories.getRepositoryFor(Storage.class).get();
            List<OperationBlankField> blankFields = blank.getFields();
            Storage storage = storageRepository.getOne(dto.getId());

            for (OperationDto operation : dto.getOperations()) {
                ThermometricWell obj = chainRepository.getOne(operation.getObject().getObjectId());
                ThermometricWellMeasurement measurement = new ThermometricWellMeasurement();
                measurement.setLoadDate(new Timestamp(new Date().getTime()));
                measurement.setThermometricWell(obj);
                measurement.setStorage(storage);
                measurement.setUserSid(userSid);
                measurement.setVerificationDate(new Timestamp(operation.getDate().getTime()));

                List<ThermometricWellMeasurementDepth> depths = new ArrayList<>();

                for (OperationBlankField blankField : blankFields) {
                    PropertyAccessor myAccessor = PropertyAccessorFactory.forBeanPropertyAccess(measurement);
                    Object value = operation.getObject().getProperties().stream().filter(s -> s.getFieldId().equals(blankField.getId())).findFirst().get().getValue();

                    if (blankField.getAdditionalNumber() != null) {
                        depths.add(new ThermometricWellMeasurementDepth(StringUtils.hasText(value.toString())
                                ? Double.parseDouble(value.toString().replace(',', '.'))
                                : null,
                                blankField.getAdditionalNumber(),
                                measurement));
                    } else {
                        setValueToProperty(value, blankField, myAccessor);
                    }
                }

                measurement.setThermometricWellMeasurementDepths(depths);
                measurement.setOperationBlank(blank);
                obj.getMeasurements().add(measurement);
                chainRepository.save(obj);
            }

            return null;
        });
    }

    /**
     * Получение списка измерений по идентификатору объекта с пагинацией
     *
     * @param objectId - идентификатор объекта
     * @param page     - номер страницы(начинается с 0)
     * @param size     - количество элементов на странице
     * @return Страницу с измерениями с типом {@link IMeasurementDto } элементов на страницы
     */
    @Override
    protected Page<? extends BaseOperation> getOperationHistory(UUID objectId, int page, int size, String sortBy, Direction direction, Specification<? extends BaseOperation> spec) {
        ThermometricWellOperationRepository chainRepository = (ThermometricWellOperationRepository) repositories.getRepositoryFor(operationClass).get();

        return chainRepository.findAll(
                (Specification<ThermometricWellOperation>) spec,
                PageRequest.of(page, size, direction, sortBy));
    }

    /**
     * Удаление списка измерений по идентификатору объекта
     *
     * @param objectId           - идентификатор объекта
     * @param listMeasurementDto - список измерений
     * @return список идентификаторов удаленных измерений
     */
    @Override
    protected List<UUID> deleteOperations(UUID objectId, List<MeasurementDto> listMeasurementDto) {
        return transactionalHandler.runInTransaction(() -> {
            List<UUID> listVisualInspectionId = new ArrayList<>();
            List<UUID> listMeasurementId = new ArrayList<>();

            listMeasurementDto.forEach(m -> {
                        if (m.getIsVisualInspection() != null && m.getIsVisualInspection().booleanValue()) {
                            listVisualInspectionId.add(m.getId());
                        } else {
                            listMeasurementId.add(m.getId());
                        }
                    }
            );

            if (!listMeasurementId.isEmpty()) {
                ThermometricWellMeasurementRepository chainRepository = (ThermometricWellMeasurementRepository) repositories.getRepositoryFor(measurementClass).get();
                chainRepository.deleteByIdIn(listMeasurementId);
            }

            if (!listVisualInspectionId.isEmpty()) {
                GtsVisualInspectionRepository chainRepository = (GtsVisualInspectionRepository) repositories.getRepositoryFor(gtsVisualInspection).get();
                chainRepository.deleteByIdIn(listVisualInspectionId);
            }

            listMeasurementId.addAll(listVisualInspectionId);
            return listMeasurementId;
        });
    }

    @Override
    protected Boolean existsOperationByObjectIdAndVerificationDate(UUID objectId, Timestamp verificationDate) {
        return ((ThermometricWellMeasurementRepository) repositories.getRepositoryFor(measurementClass).get())
                .existsByThermometricWellIdAndVerificationDate(objectId, verificationDate);
    }

    @Override
    public List<UUID> deleteObjects(List<TreeNodeDto> listDeletedObjects) {
        return transactionalHandler.runInTransaction(() -> {
            List<UUID> listTreeNodeId = new ArrayList<>();
            List<UUID> listObjectsId = new ArrayList<>();

            listDeletedObjects.forEach(o -> {
                listTreeNodeId.add(o.getId());
                listObjectsId.add(o.getObjectId());
            });

            if (!listObjectsId.isEmpty()) {
                ThermometricWellRepository chainRepository =
                        (ThermometricWellRepository) repositories.getRepositoryFor(entityClass).get();
                chainRepository.deleteByIdIn(listObjectsId);
                bindingRepository.deleteAllByBindedIn(listTreeNodeId);
                treeNodeRepository.deleteByIdIn(listTreeNodeId);
            }

            return listObjectsId;
        });
    }

    @Override
    public HashMap<String, Integer> createUpdateDeleteMultipleObject(CudObjectsDto cudObjectsDto,
                                                                     UUID categoryId,
                                                                     UUID parentId) {
        throw new BusinessException("Не поддерживается");
    }

    @Override
    public List<Object> multipleUpdateObjects(MultipleEditUpdateDto<?> multipleEditUpdateDto) {
        throw new BusinessException("Не поддерживается");
    }

    @Override
    protected List<Object> multipleSaveWithProperties(List<Object> listDto, UUID categoryId, UUID parentId) {
        throw new BusinessException("Не поддерживается");
    }
}
