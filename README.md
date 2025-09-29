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
Термостабилизатор грунта (ТСГ), фронтенд:
<template>
  <v-container ma-0 pa-0 fluid>
    <v-form ref="form" v-model="valid" flat class="pa-8" lazy-validation>
      <v-row class="pb-3">
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiTextField
            label="Наименование"
            showTooltipLabel
            v-model="treeNode.name"
            :disabled="disabled"
            maxlength="255"
            :rules="[rules.required()]"
          />
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiSelect
            label="Тип термостабилизатора грунта"
            showTooltipLabel
            :disabled="disabledSelected"
            v-model="tsgProperties.dictGroundThermalStabilizerType"
            :items="valuesSelect.listGroundThermalStabilizerType"
          />
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiTextField
            label="Общая длина"
            showTooltipLabel
            defaultUnitMeasurement="м"
            :hint="`Число в диапазоне [0;+\u221E]`"
            v-model="tsgProperties.totalLength"
            :disabled="disabled"
            :rules="[rules.positiveNumber(), rules.numberDecimal(3)]"
          />
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiTextField
            label="Длина оребрения"
            showTooltipLabel
            defaultUnitMeasurement="м"
            :hint="`Число в диапазоне [0;+\u221E]`"
            v-model="tsgProperties.finLength"
            :disabled="disabled"
            :rules="[rules.positiveNumber(), rules.numberDecimal(3)]"
          />
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <v-tooltip top>
            <span> Конструктивные особенности </span>
            <template v-slot:activator="{ on, attrs }">
              <div v-on="on" v-bind="attrs">
                <NipiTextField
                  label="Конструктивные особенности"
                  v-model="tsgProperties.constructionFeatures"
                  :disabled="disabled"
                />
              </div>
            </template>
          </v-tooltip>
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiSelect
            label="Показатель эффективности (коэффициент)"
            showTooltipLabel
            :disabled="disabledSelected"
            v-model="tsgProperties.dictPerformanceIndicator"
            :items="valuesSelect.listPerformanceIndicator"
          />
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiSelect
            label="Теплоизоляционная муфта, мм"
            showTooltipLabel
            defaultUnitMeasurement="мм"
            :disabled="disabledSelected"
            v-model="tsgProperties.dictHeatInsulatingSleeve"
            :items="valuesSelect.listHeatInsulatingSleeve"
          />
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiSelect
            label="Тип климатического исполнения"
            showTooltipLabel
            :disabled="disabledSelected"
            v-model="tsgProperties.dictClimaticVersionType"
            :items="valuesSelect.listClimaticVersionType"
          />
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiTextField
            label="Площадь конденсаторной (condenser) части с оребрением (Паспорт СОУ)"
            showTooltipLabel
            defaultUnitMeasurement="м²"
            :hint="`Число в диапазоне [0;100]`"
            v-model="tsgProperties.condenserPartFinsArea"
            :disabled="disabled"
            :rules="[
              rules.positiveNumber(),
              rules.max(100),
              rules.numberDecimal(3),
            ]"
          />
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiTextField
            label="Площадь испарительной (evaporator) части"
            showTooltipLabel
            defaultUnitMeasurement="м²"
            :hint="`Число в диапазоне [0;100]`"
            v-model="tsgProperties.evaporatorPartArea"
            :disabled="disabled"
            :rules="[
              rules.positiveNumber(),
              rules.max(100),
              rules.numberDecimal(3),
            ]"
          />
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiTextField
            label="Термическое сопротивление СОУ"
            showTooltipLabel
            defaultUnitMeasurement="(°С м²)/Вт"
            :hint="`Число в диапазоне [0;10]`"
            v-model="tsgProperties.seasonalCoolerThermalResistance"
            :disabled="disabled"
            :rules="[
              rules.positiveNumber(),
              rules.max(10),
              rules.numberDecimal(3),
            ]"
          />
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiTextField
            label="Разница температур для активации СОУ, °С"
            showTooltipLabel
            defaultUnitMeasurement="°С"
            :hint="`Число в диапазоне [0;100]`"
            v-model="
              tsgProperties.seasonalCoolerActivationTemperatureDifference
            "
            :disabled="disabled"
            :rules="[
              rules.positiveNumber(),
              // todo решить удалить или вернуть. скрыто по GK-734
              // rules.minWithFieldName(
              //   tsgProperties.seasonalCoolerDeactivationTemperatureDifference,
              //   `разницы деактивации`
              // ),
              rules.max(100),
              rules.numberDecimal(3),
            ]"
          />
        </v-col>
        <!-- todo решить удалить или вернуть. скрыто по GK-734 -->
        <!--        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">-->
        <!--          <v-tooltip bottom>-->
        <!--            <span> Разница температур для деактивации СОУ, °С </span>-->
        <!--            <template v-slot:activator="{ on, attrs }">-->
        <!--              <div v-on="on" v-bind="attrs">-->
        <!--                <NipiTextField-->
        <!--                  label="Разница температур для деактивации СОУ, °С"-->
        <!--                  :hint="`Число в диапазоне [0;100], должно быть меньше-->
        <!--              разницы активации`"-->
        <!--                  v-model="-->
        <!--                    tsgProperties.seasonalCoolerDeactivationTemperatureDifference-->
        <!--                  "-->
        <!--                  :disabled="disabled"-->
        <!--                  :rules="[-->
        <!--                    rules.positiveNumber(),-->
        <!--                    rules.maxWithFieldName(-->
        <!--                      tsgProperties.seasonalCoolerActivationTemperatureDifference,-->
        <!--                      `разницы активации`-->
        <!--                    ),-->
        <!--                    rules.max(100),-->
        <!--                    rules.numberDecimal(2),-->
        <!--                  ]"-->
        <!--                />-->
        <!--              </div>-->
        <!--            </template>-->
        <!--          </v-tooltip>-->
        <!--        </v-col>-->
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiTextField
            label="Длина надземной части (оребрение + часть трубы)"
            showTooltipLabel
            defaultUnitMeasurement="м"
            :hint="`Число в диапазоне [0;+\u221E]`"
            v-model="tsgProperties.upperPartLength"
            :disabled="disabled"
            :rules="[rules.positiveNumber(), rules.numberDecimal(3)]"
          />
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiTextField
            label="Диаметр трубы испарителя, м"
            showTooltipLabel
            defaultUnitMeasurement="м"
            :hint="`Число в диапазоне [0;1]`"
            v-model="tsgProperties.evaporatorPipeDiameter"
            :disabled="disabled"
            :rules="[
              rules.positiveNumber(),
              rules.max(1),
              rules.numberDecimal(3),
            ]"
          />
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
                  :value="tsgProperties.visualInspection.dateInspection"
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
                    tsgProperties.visualInspection &&
                    tsgProperties.visualInspection.gtmElementState
                      ? tsgProperties.visualInspection.gtmElementState.name
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

export default {
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
      tsgProperties: {
        totalLength: null,
        finLength: null,
        constructionFeatures: null,
        condenserPartFinsArea: null,
        evaporatorPartArea: null,
        seasonalCoolerThermalResistance: null,
        seasonalCoolerActivationTemperatureDifference: null,
        evaporatorPipeDiameter: null,
        // todo решить удалить или вернуть. скрыто по GK-734
        // seasonalCoolerDeactivationTemperatureDifference: null,
        upperPartLength: null,
        dictGroundThermalStabilizerType: {
          id: null,
          name: null,
        },
        dictPerformanceIndicator: {
          id: null,
          name: null,
        },
        dictHeatInsulatingSleeve: {
          id: null,
          name: null,
        },
        dictClimaticVersionType: {
          id: null,
          name: null,
        },
        visualInspection: {
          dateInspection: null,
          gtmElementState: { id: null, name: null },
        },
      },
      treeNode: { name: null },
      valuesSelect: {
        listGroundThermalStabilizerType: [],
        listPerformanceIndicator: [],
        listHeatInsulatingSleeve: [],
        listClimaticVersionType: [],
      },
      rules: validationRules,
      categories: CategoryUtils,
    };
  },
  mounted() {
    if (this.data) {
      this.tsgProperties = this.data.properties;
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
      this.tsgProperties = val.properties;
      this.treeNode = val.treeNode;
      this.addInitialValueSelect(val.properties);
    },
  },

  methods: {
    /**
     * Проверка валидности полей
     * @returns {boolean} возвращает валидные или нет значения полей формы
     */
    validate() {
      return this.$refs.form.validate();
    },

    /**
     * Сброс значения всех полей
     */
    reset() {
      this.$refs.form.reset();
    },

    /**
     * Добавления текущих значений в массив значений полей select
     * для того что бы текущих значений отображались
     * @param properties
     */
    addInitialValueSelect(properties) {
      this.valuesSelect.listGroundThermalStabilizerType.push(
        properties.dictGroundThermalStabilizerType
      );
      this.valuesSelect.listPerformanceIndicator.push(
        properties.dictPerformanceIndicator
      );
      this.valuesSelect.listHeatInsulatingSleeve.push(
        properties.dictHeatInsulatingSleeve
      );
      this.valuesSelect.listClimaticVersionType.push(
        properties.dictClimaticVersionType
      );
    },

    /**
     * Получение всех возможных значений для полей select,
     *  при редактирование
     */
    getValueForSelected() {
      this.axios
        .get("/tsg/values/select")
        .then((response) => {
          this.valuesSelect = response.data.data;
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
  },
};
</script>
Термостабилизатор грунта (ТСГ), бэкенд:
/**
 * Термостабилизатор грунта (ТСГ)
 */
@Entity
@Table(name = "ground_thermal_stabilizer")
@Getter
@Setter
@NoArgsConstructor
public class GroundThermalStabilizer extends BaseId {
    /**
     * Общая длина, м
     */
    @Column(name = "total_length")
    private Double totalLength;

    /**
     * Длина оребрения, м
     */
    @Column(name = "fin_length")
    private Double finLength;

    /**
     * Номер термостабилизатора грунта
     */
    @Column(name = "gts_number")
    private Double gtsNumber;

    /**
     * ID типа термостабилизатора грунта
     */
    @ManyToOne
    @JoinColumn(name = "dict_ground_thermal_stabilizer_type_id")
    private DictGroundThermalStabilizerType dictGroundThermalStabilizerType;

    /**
     * ID показателя эффективности (коэффициента)
     */
    @ManyToOne
    @JoinColumn(name = "dict_performance_indicator_id")
    private DictPerformanceIndicator dictPerformanceIndicator;

    /**
     * ID значения теплоизоляционной муфты (мм)
     */
    @ManyToOne
    @JoinColumn(name = "dict_heat_insulating_sleeve_id")
    private DictHeatInsulatingSleeve dictHeatInsulatingSleeve;

    /**
     * ID типа климатического исполнения
     */
    @ManyToOne
    @JoinColumn(name = "dict_climatic_version_type_id")
    private DictClimaticVersionType dictClimaticVersionType;

    /**
     * Площадь конденсаторной (condenser) части с оребрением, м²
     */
    @Column(name = "condenser_part_fins_area")
    private Double condenserPartFinsArea;

    /**
     * Площадь испарительной (evaporator) части, м²
     */
    @Column(name = "evaporator_part_area")
    private Double evaporatorPartArea;

    /**
     * Термическое сопротивление СОУ, (°С м²)/Вт
     */
    @Column(name = "seasonal_cooler_thermal_resistance")
    private Double seasonalCoolerThermalResistance;

    /**
     * Разница температур для активации СОУ, °С
     */
    @Column(name = "seasonal_cooler_activation_temperature_difference")
    private Double seasonalCoolerActivationTemperatureDifference;

    /**
     * Разница температур для деактивации СОУ, °С
     * todo решить удалить или вернуть. пока не используем по GK-734
     */
    @Column(name = "seasonal_cooler_deactivation_temperature_difference")
    private Double seasonalCoolerDeactivationTemperatureDifference;

    /**
     * Длина надземной части (оребрение + часть трубы), м (Паспорт СОУ)
     */
    @Column(name = "upper_part_length")
    private Double upperPartLength;

    @Column(name = "evaporator_pipe_diameter")
    private Double evaporatorPipeDiameter;

    /**
     * Конструктивные особенности ТСГ
     * */
    @Column(name = "construction_features")
    private String constructionFeatures;

    public GroundThermalStabilizer(GroundThermalStabilizer original) {
        this.setTotalLength(original.getTotalLength());
        this.setFinLength(original.getFinLength());
        this.setGtsNumber(original.getGtsNumber());
        this.setDictGroundThermalStabilizerType(original.getDictGroundThermalStabilizerType());
        this.setDictPerformanceIndicator(original.getDictPerformanceIndicator());
        this.setDictHeatInsulatingSleeve(original.getDictHeatInsulatingSleeve());
        this.setDictClimaticVersionType(original.getDictClimaticVersionType());
        this.setCondenserPartFinsArea(original.getCondenserPartFinsArea());
        this.setEvaporatorPartArea(original.getEvaporatorPartArea());
        this.setSeasonalCoolerThermalResistance(original.getSeasonalCoolerThermalResistance());
        this.setSeasonalCoolerActivationTemperatureDifference(original.getSeasonalCoolerActivationTemperatureDifference());
        this.setSeasonalCoolerActivationTemperatureDifference(original.getSeasonalCoolerActivationTemperatureDifference());
        this.setUpperPartLength(original.getUpperPartLength());
        this.setEvaporatorPipeDiameter(original.getEvaporatorPipeDiameter());
        this.setConstructionFeatures(original.getConstructionFeatures());
    }
}
@Getter
@NoArgsConstructor
@GroundThermalStabilizerPropertiesDtoAnnotationValidator(groups = CardGroup.class)
public class GroundThermalStabilizerPropertiesDto {
    /**
     * Идентификатор объекта
     */
    @JsonProperty("id")
    private UUID id;

    /**
     * Общая длина, м
     */
    @JsonProperty("totalLength")
    @DescriptionField("Общая длина, м")
    @Min(value = 0, message = "Значение должно быть положительным")
    @NumberDecimal(decimal = 3, message = "Количество знаков после запятой не должно превышать 3")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double totalLength;

    /**
     * Длина оребрения, м
     */
    @JsonProperty("finLength")
    @DescriptionField("Длина оребрения, м")
    @Min(value = 0, message = "Значение должно быть положительным")
    @NumberDecimal(decimal = 3, message = "Количество знаков после запятой не должно превышать 3")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double finLength;

    /**
     * Тип термостабилизатора грунта
     */
    @JsonProperty("dictGroundThermalStabilizerType")
    @DescriptionField("Тип термостабилизатора грунта")
    private DictGroundThermalStabilizerType dictGroundThermalStabilizerType;

    /**
     * Показатель эффективности (коэффициент)
     */
    @JsonProperty("dictPerformanceIndicator")
    @DescriptionField("Показатель эффективности (коэффициент)")
    private DictPerformanceIndicator dictPerformanceIndicator;

    /**
     * Значения теплоизоляционной муфты (мм)
     */
    @JsonProperty("dictHeatInsulatingSleeve")
    @DescriptionField("Значения теплоизоляционной муфты (мм)")
    private DictHeatInsulatingSleeve dictHeatInsulatingSleeve;

    /**
     * Тип климатического исполнения
     */
    @JsonProperty("dictClimaticVersionType")
    @DescriptionField("Тип климатического исполнения")
    private DictClimaticVersionType dictClimaticVersionType;

    /**
     * Площадь конденсаторной (condenser) части с оребрением, м²
     */
    @JsonProperty("condenserPartFinsArea")
    @DescriptionField("Площадь конденсаторной (condenser) части с оребрением, м²")
    @Min(value = 0, message = "Значение должно быть положительным")
    @Max(value = 100, message = "Значение не должно превышать 100")
    @NumberDecimal(decimal = 3, message = "Количество знаков после запятой не должно превышать 3")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double condenserPartFinsArea;

    /**
     * Площадь испарительной (evaporator) части, м²
     */
    @JsonProperty("evaporatorPartArea")
    @DescriptionField("Площадь испарительной (evaporator) части, м²")
    @Min(value = 0, message = "Значение должно быть положительным")
    @Max(value = 100, message = "Значение не должно превышать 100")
    @NumberDecimal(decimal = 3, message = "Количество знаков после запятой не должно превышать 3")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double evaporatorPartArea;

    /**
     * Термическое сопротивление СОУ, (°С м²)/Вт
     */
    @JsonProperty("seasonalCoolerThermalResistance")
    @DescriptionField("Термическое сопротивление СОУ, (°С м²)/Вт")
    @Min(value = 0, message = "Значение должно быть положительным")
    @Max(value = 10, message = "Значение не должно превышать 10")
    @NumberDecimal(decimal = 3, message = "Количество знаков после запятой не должно превышать 3")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double seasonalCoolerThermalResistance;

    /**
     * Разница температур для активации СОУ, °С
     */
    @JsonProperty("seasonalCoolerActivationTemperatureDifference")
    @DescriptionField("Разница температур для активации СОУ, °С")
    @Min(value = 0, message = "Значение должно быть положительным")
    @Max(value = 100, message = "Значение не должно превышать 100")
    @NumberDecimal(decimal = 3, message = "Количество знаков после запятой не должно превышать 3")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double seasonalCoolerActivationTemperatureDifference;

    // todo решить удалить или вернуть. скрыто по GK-734
//    /**
//     * Разница температур для деактивации СОУ, °С
//     */
//    @JsonProperty("seasonalCoolerDeactivationTemperatureDifference")
//    @DescriptionField("Разница температур для деактивации СОУ, °С")
//    @Min(value = 0, message = "Значение должно быть положительным")
//    @Max(value = 100, message = "Значение не должно превышать 100")
//    @NumberDecimal(decimal = 2, message = "Количество знаков после запятой не должно превышать 2")
//    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
//    private Double seasonalCoolerDeactivationTemperatureDifference;

    /**
     * Длина надземной части (оребрение + часть трубы), м (Паспорт СОУ)
     */
    @JsonProperty("upperPartLength")
    @DescriptionField("Длина надземной части (оребрение + часть трубы), м (Паспорт СОУ)")
    @Min(value = 0, message = "Значение должно быть положительным")
    @NumberDecimal(decimal = 3, message = "Количество знаков после запятой не должно превышать 3")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double upperPartLength;

    @JsonProperty("evaporatorPipeDiameter")
    @DescriptionField("Длина трубы испарителя, м")
    @Min(value = 0, message = "Значение должно быть положительным")
    @Max(value = 1, message = "Значение не должно превышать 1")
    @NumberDecimal(decimal = 3, message = "Количество знаков после запятой не должно превышать 3")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double evaporatorPipeDiameter;

    /**
     * Последний технический осмотр
     */
    @JsonProperty("visualInspection")
    private GtsVisualInspectionDto visualInspection;

    /**
     * Конструктивные особенности ТСГ
     * */
    @JsonProperty("constructionFeatures")
    @DescriptionField("Конструктивные особенности")
    @Size(max = 1000, message = "Количество символов не должно превышать 1000")
    private String constructionFeatures;

    public GroundThermalStabilizerPropertiesDto(GroundThermalStabilizer tsg, GtsVisualInspectionDto visualInspection) {
        this.id = tsg.getId();
        this.totalLength = tsg.getTotalLength();
        this.finLength = tsg.getFinLength();
        this.dictGroundThermalStabilizerType = tsg.getDictGroundThermalStabilizerType();
        this.dictPerformanceIndicator = tsg.getDictPerformanceIndicator();
        this.dictHeatInsulatingSleeve = tsg.getDictHeatInsulatingSleeve();
        this.dictClimaticVersionType = tsg.getDictClimaticVersionType();
        this.condenserPartFinsArea = tsg.getCondenserPartFinsArea();
        this.evaporatorPartArea = tsg.getEvaporatorPartArea();
        this.seasonalCoolerThermalResistance = tsg.getSeasonalCoolerThermalResistance();
        this.seasonalCoolerActivationTemperatureDifference = tsg.getSeasonalCoolerActivationTemperatureDifference();
        // todo решить удалить или вернуть. скрыто по GK-734
        // this.seasonalCoolerDeactivationTemperatureDifference = tsg.getSeasonalCoolerDeactivationTemperatureDifference();
        this.upperPartLength = tsg.getUpperPartLength();
        this.evaporatorPipeDiameter = tsg.getEvaporatorPipeDiameter();
        this.constructionFeatures = tsg.getConstructionFeatures();

        this.visualInspection = visualInspection;
    }
}
public class GroundThermalStabilizerCreatorHandler extends BaseCreatorHandler {
    private final Class<GroundThermalStabilizer> entityClass = GroundThermalStabilizer.class;
    private final Class<AddObjectDto> saveDtoClass = AddObjectDto.class;
    private final Class<AddMultipleObjectsDto> saveAddMultipleObjectsDtoClass = AddMultipleObjectsDto.class;
    private final Class<CopyObjectDto> copyDtoClass = CopyObjectDto.class;
    private final Class<GtsVisualInspection> gtsVisualInspection = GtsVisualInspection.class;
    private final Class<GroundThermalStabilizerOperation> operationClass = GroundThermalStabilizerOperation.class;

    public GroundThermalStabilizerCreatorHandler(Repositories repositories, TransactionalHandler transactionalHandler, AppServiceForObjectCreatorChain appServiceForObjectCreatorChain) {
        super(repositories, transactionalHandler, appServiceForObjectCreatorChain);
    }

    @Override
    protected boolean isCategoryOfObject(UUID categoryId) {
        return categoryId.equals(Category.Constants.TSG_ID);
    }

    @Override
    protected TreeNode save(Object dto) {
        return transactionalHandler.runInTransaction(() -> {
            AddObjectDto objectDto = castDto(dto, saveDtoClass);
            checkCodeDuplication(objectDto.getCode());
            GroundThermalStabilizerRepository chainRepository = (GroundThermalStabilizerRepository) repositories.getRepositoryFor(entityClass).get();
            GroundThermalStabilizer object = chainRepository.save(new GroundThermalStabilizer());

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
            GroundThermalStabilizerRepository chainRepository = (GroundThermalStabilizerRepository) repositories.getRepositoryFor(entityClass).get();
            GroundThermalStabilizer original = chainRepository.findById(objectDto.getCopiedId()).orElseThrow(
                    () -> new BusinessException("Не найден копируемый объект"));
            GroundThermalStabilizer object = chainRepository.save(new GroundThermalStabilizer(original));

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
            GroundThermalStabilizerRepository chainRepository =
                    (GroundThermalStabilizerRepository) repositories.getRepositoryFor(entityClass).get();

            List<GroundThermalStabilizer> groundThermalStabilizerForSave = new ArrayList<>();
            for (int i = 0; i < dto.getCount(); i++) {
                groundThermalStabilizerForSave.add(new GroundThermalStabilizer());
            }

            List<GroundThermalStabilizer> savedObjects = chainRepository.saveAll(groundThermalStabilizerForSave);

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
            GroundThermalStabilizerCardDto tsgCardDto = castDto(dto, GroundThermalStabilizerCardDto.class);

            GroundThermalStabilizerPropertiesDto propertiesDto = tsgCardDto.getProperties();
            TreeNodeDto treeNodeDto = tsgCardDto.getTreeNode();

            GroundThermalStabilizerRepository chainRepository =
                    (GroundThermalStabilizerRepository) repositories.getRepositoryFor(entityClass).get();

            GroundThermalStabilizer tsg = chainRepository.findById(propertiesDto.getId())
                    .orElseThrow(() -> new BusinessException("Не удалось найти объект"));

            tsg.setTotalLength(propertiesDto.getTotalLength());
            tsg.setFinLength(propertiesDto.getFinLength());
            tsg.setDictGroundThermalStabilizerType(propertiesDto.getDictGroundThermalStabilizerType());
            tsg.setDictPerformanceIndicator(propertiesDto.getDictPerformanceIndicator());
            tsg.setDictHeatInsulatingSleeve(propertiesDto.getDictHeatInsulatingSleeve());
            tsg.setDictClimaticVersionType(propertiesDto.getDictClimaticVersionType());
            tsg.setCondenserPartFinsArea(propertiesDto.getCondenserPartFinsArea());
            tsg.setEvaporatorPartArea(propertiesDto.getEvaporatorPartArea());
            tsg.setSeasonalCoolerThermalResistance(propertiesDto.getSeasonalCoolerThermalResistance());
            tsg.setSeasonalCoolerActivationTemperatureDifference(propertiesDto.getSeasonalCoolerActivationTemperatureDifference());
            // todo решить удалить или вернуть. скрыто по GK-734
            //tsg.setSeasonalCoolerDeactivationTemperatureDifference(propertiesDto.getSeasonalCoolerDeactivationTemperatureDifference());
            tsg.setUpperPartLength(propertiesDto.getUpperPartLength());
            tsg.setEvaporatorPipeDiameter(propertiesDto.getEvaporatorPipeDiameter());

            TreeNode treeNode = treeNodeRepository.findById(treeNodeDto.getId())
                    .orElseThrow(() -> new BusinessException("Не удалось найти узел дерева объектов"));
            treeNode.setName(treeNodeDto.getName());

            TreeNode savedTreeNode = treeNodeRepository.save(treeNode);
            GroundThermalStabilizer savedTsg = chainRepository.save(tsg);
            GtsVisualInspectionDto gtsVisualInspectionDto =
                    this.appServiceForObjectCreatorChain.getGtsVisualInspectionService().getLastVisualInspection(tsgCardDto.getTreeNode().getObjectId());

            return new GroundThermalStabilizerCardDto(new TreeNodeDto(savedTreeNode), new GroundThermalStabilizerPropertiesDto(savedTsg, gtsVisualInspectionDto));
        });
    }

    @Override
    protected TreeNodeDto delete(UUID objectId) {
        return transactionalHandler.runInTransaction(() -> {
            GroundThermalStabilizerRepository chainRepository = (GroundThermalStabilizerRepository) repositories.getRepositoryFor(entityClass).get();
            chainRepository.delete(chainRepository.getOne(objectId));
            TreeNode treeNode = treeNodeRepository.getByObjectId(objectId);
            bindingRepository.deleteAllByBinded(treeNode.getId());
            treeNodeRepository.delete(treeNode);
            return new TreeNodeDto(treeNode);
        });
    }

    @Override
    protected ImportableDto importCard(ImportableDto dto, OperationBlank blank) {
        return transactionalHandler.runInTransaction(() -> {
            GroundThermalStabilizerRepository chainRepository = (GroundThermalStabilizerRepository) repositories.getRepositoryFor(entityClass).get();
            List<OperationBlankField> blankFields = blank.getFields();

            for (OperationDto operation : dto.getOperations()) {
                GroundThermalStabilizer obj = chainRepository.getOne(operation.getObject().getObjectId());

                fillObjectPropertiesFromOperationBlankFields(operation,
                        blankFields,
                        chainRepository,
                        obj);

                chainRepository.save(obj);
            }

            return null;
        });
    }

    /**
     * Получение списка визуальных обследований по идентификатору объекта с пагинацией
     *
     * @param objectId - идентификатор объекта
     * @param page     - номер страницы( начинается с 0)
     * @param size     - количесвто элементов на странице
     * @return Страницу с визуальными обследованиями c типом {@link IMeasurementDto } элеметов на страниицы
     */
    @Override
    protected Page<? extends BaseOperation> getOperationHistory(UUID objectId, int page, int size, String sortBy, Direction direction, Specification<? extends BaseOperation> spec) {
        GroundThermalStabilizerOperationRepository chainRepository = (GroundThermalStabilizerOperationRepository) repositories.getRepositoryFor(operationClass).get();

        return chainRepository.findAll(
                (Specification<GroundThermalStabilizerOperation>) spec,
                PageRequest.of(page, size, direction, sortBy));
    }

    /**
     * Удаление списка измерений по идентификатору объекта
     *
     * @param objectId           - идентификатор объекта
     * @param listMeasurementDto - список визуальных обследований
     * @return список идентификаторов удаленных визуальных обследований
     */
    @Override
    protected List<UUID> deleteOperations(UUID objectId, List<MeasurementDto> listMeasurementDto) {
        return transactionalHandler.runInTransaction(() -> {
            List<UUID> listVisualInspectionId = new ArrayList<>();

            listMeasurementDto.forEach(m -> listVisualInspectionId.add(m.getId()));

            if (!listVisualInspectionId.isEmpty()) {
                GtsVisualInspectionRepository chainRepository = (GtsVisualInspectionRepository) repositories.getRepositoryFor(gtsVisualInspection).get();
                chainRepository.deleteByIdIn(listVisualInspectionId);
            }

            return listVisualInspectionId;
        });
    }

    @Override
    protected Boolean existsOperationByObjectIdAndVerificationDate(UUID objectId, Timestamp verificationDate) {
        throw new BusinessException("Не поддерживается");
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
                GroundThermalStabilizerRepository chainRepository = (GroundThermalStabilizerRepository) repositories.getRepositoryFor(entityClass).get();
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
