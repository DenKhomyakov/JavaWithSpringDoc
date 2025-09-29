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
Общий файл свойств ProjectProperties.vue:
<template>
  <v-container ma-0 pa-0 fluid>
    <v-form ref="form" v-model="valid" flat class="pa-8" lazy-validation>
      <v-row class="pb-3">
        <v-col class="py-2">
          <v-tooltip bottom>
            <span> Наименование </span>
            <template v-slot:activator="{ on, attrs }">
              <div v-on="on" v-bind="attrs" class="object-info">
                <NipiTextField
                  label="Название проекта / площадки"
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
          <v-tooltip bottom>
            <span> Шифр проекта </span>
            <template v-slot:activator="{ on, attrs }">
              <div v-on="on" v-bind="attrs" class="object-info">
                <NipiTextField
                  label="Шифр проекта"
                  v-model="projectProperties.cipher"
                  :disabled="disabled"
                  :rules="[rules.required()]"
                />
              </div>
            </template>
          </v-tooltip>
        </v-col>
      </v-row>
      <v-row class="pl-1">
        <v-col cols="12">
          <div class="subheader-card pb-2">Координаты центра площадки</div>
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <v-tooltip bottom>
            <span> Центр площадки, X{{ getCaption() }} </span>
            <template v-slot:activator="{ on, attrs }">
              <div
                v-on="on"
                v-bind="attrs"
                :class="{ readonly: projectProperties.hasGeometry }"
              >
                <NipiTextField
                  type="number"
                  label="Центр площадки, X"
                  hint="Число в диапазоне [-∞; +∞]"
                  v-model="projectProperties.centerY"
                  :disabled="disabled || projectProperties.hasGeometry"
                />
              </div>
            </template>
          </v-tooltip>
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <v-tooltip bottom>
            <span> Центр площадки, Y{{ getCaption() }} </span>
            <template v-slot:activator="{ on, attrs }">
              <div
                v-on="on"
                v-bind="attrs"
                :class="{ readonly: projectProperties.hasGeometry }"
              >
                <NipiTextField
                  type="number"
                  label="Центр площадки, Y"
                  hint="Число в диапазоне [-∞; +∞]"
                  v-model="projectProperties.centerX"
                  :disabled="disabled || projectProperties.hasGeometry"
                />
              </div>
            </template>
          </v-tooltip>
        </v-col>
      </v-row>
    </v-form>
    <v-container v-if="loader.isLoading">
      <NipiLoader
        loaderSize="100"
        :overlayOpacity="0.3"
        :indeterminate="loader.indeterminate"
      />
    </v-container>
    <div v-else>
      <v-card tile>
        <v-data-table
          class="table-scroll"
          :headers="headers"
          :items="statistic"
          :search="search"
          disable-pagination
          hide-default-footer
        >
          <template v-slot:top>
            <v-toolbar flat color="headerBackground" :height="toolbarHeight">
              <v-toolbar-title class="mr-auto header-font-color">
                Статистика
              </v-toolbar-title>
              <v-toolbar-items>
                <NipiSearchTextField v-model="search" class="py-1" />
              </v-toolbar-items>
            </v-toolbar>
          </template>
        </v-data-table>
      </v-card>
    </div>
  </v-container>
</template>
<script>
import CategoryUtils from "@/assets/js/utils/categoryUtils";
import validationRules from "@/assets/js/utils/validationRulesUtils";
import NipiSearchTextField from "@/components/NipiComponents/NipiSearchTextField";
import scssVar from "@/styles/exportVarToJs.scss";

export default {
  components: { NipiSearchTextField },
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
      projectProperties: {
        cipher: null,
        centerX: null,
        centerY: null,
        code: null,
        hasGeometry: false,
      },
      treeNode: { name: null },
      rules: validationRules,
      categories: CategoryUtils,
      loader: {
        isLoading: true,
        indeterminate: true,
      },
      toolbarHeight: scssVar.toolbarHeight,
      search: "",
      headers: [
        { text: "Категория", value: "name", width: "36%" },
        {
          text: "Количество элементов",
          value: "data",
          width: "36%",
          align: "center",
        },
      ],
      statistic: [],
    };
  },
  mounted() {
    if (this.data) {
      this.projectProperties = this.data.properties;
      this.treeNode = this.data.treeNode;
      this.getCountCategory(this.data.treeNode.id);
    }
  },

  watch: {
    data(val) {
      this.projectProperties = val.properties;
      this.treeNode = val.treeNode;
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

    getCaption() {
      return this.projectProperties.hasGeometry
        ? ". Для редактирования геометрии перейдите на карту"
        : "";
    },

    getCountCategory(treeLevelId) {
      this.axios
        .get("/objectTree/getCountAllCategories/" + treeLevelId + "/")
        .then((response) => {
          let parsedobj = JSON.parse(JSON.stringify(response));
          let answer = JSON.parse(
            JSON.stringify(parsedobj.data.countObjectsInCategories)
          );
          this.statistic = answer;
          this.loader.isLoading = false;
        })
        .catch((thrown) => {
          //отключаем индикацию загрузки
          this.loader.indeterminate = false;
          this.setSnack({
            message: "Не удалось получить статистические данные",
            color: "error",
          });
          console.error(thrown.message);
        });
    },
  },
};
</script>

<style scoped>
.object-info {
  color: black;
}

.readonly >>> .theme--light.v-input--is-disabled input {
  color: darkgray;
}

.subheader-card {
  color: black;
  border-bottom: 1px solid #888888;
  font-size: 18px;
}
</style>

<style scoped lang="scss">
.table-scroll ::v-deep th {
  height: #{$toolbar-height} !important;
}

.table-scroll ::v-deep tbody {
  display: block;
  overflow-y: auto;
  height: calc(100vh - #{$header-height} - (#{$toolbar-height} * 10.88));
}

.table-scroll ::v-deep tr {
  display: table;
  width: 100%;
  table-layout: fixed;
}
</style>
Ещё один файл с настройками (AddObjectControl.vue):
<template>
  <v-form ref="form" v-model="valid" flat lazy-validation>
    <v-text-field
      outlined
      required
      autofocus
      label="Название"
      v-model="object.name"
      :rules="[rules.required()]"
    />
    <v-row>
      <v-col align-self="stretch">
        <v-text-field
          reverse
          disabled
          :value="code"
          persistent-hint
          placeholder=" "
          label="Код объекта"
          hint="Составной код объекта по классификатору"
        />
      </v-col>
    
      <v-col align-self="stretch">
        <v-text-field
          required
          type="number"
          persistent-hint
          hint="порядковый номер"
          v-model="object.orderCode"
          :rules="[rules.positiveNumber()]"
          v-if="!isBuilding && !isPipelines"
        />

        <v-text-field
          v-else
          required
          persistent-hint
          :rules="buildingRules"
          v-model="object.orderCode"
          hint="№ позиции по ген.плану"
        />
      </v-col>
    </v-row>

    <v-row v-if="isBaseCenter">
      <v-col align-self="stretch">
        <v-text-field
          type="number"
          persistent-hint
          hide-spin-buttons
          hint="X координата"
          v-model="object.centerY"
        />
      </v-col>

      <v-col align-self="stretch">
        <v-text-field
          type="number"
          persistent-hint
          hide-spin-buttons
          hint="Y координата"
          v-model="object.centerX"
        />
      </v-col>
    </v-row>

    <div v-if="isPipelines">
      <v-switch dense v-model="isSections" label="Деление на участки" />
      <AddSectionsControl
        v-if="isSections"
        ref="addSectionsControl"
        :parentCode="this.code + this.object.orderCode"
      />
      <AddPicketsControl
        v-else
        ref="addPicketsControl"
        :parentCode="this.code + this.object.orderCode"
      />
    </div>
  </v-form>
</template>

<script>
import CategoryUtils from "@/assets/js/utils/categoryUtils";
import RoutesUtils from "@/assets/js/utils/routesUtils";
import TreeUtils from "@/assets/js/utils/treeUtils";
import validationRules from "@/assets/js/utils/validationRulesUtils";
import { mapGetters, mapMutations } from "vuex";
import AddPicketsControl from "./AddPicketsPipelineControl";
import AddSectionsControl from "./AddSectionsPipelineControl";

export default {
  name: "AddObjectControl",
  props: ["category"],

  components: {
    AddPicketsControl,
    AddSectionsControl,
  },

  data() {
    return {
      isSections: true,
      object: {
        category: {
          name: "",
          id: undefined,
        },
        code: "",
        name: null,
        centerX: null,
        centerY: null,
        orderCode: null,
      },
      valid: true,
      isBuilding: false,
      isPipelines: false,
      isBaseCenter: false,
      rules: validationRules,
      selectedCategoryId: null,
      buildingRules: [
        (v) => !!v || "Поле обязательное к заполнению",
        (v) =>
          (v &&
            v.toString().replace(/[a-zA-Zа-яА-Я0-9./]/g, "").length === 0) ||
          "№ позиции по ген.плану может содержать только числа и буквы (не более 3) и разделители (. или /)",
        (v) =>
          (v && v.toString().replace(/[0-9./]/g, "").length <= 3) ||
          "Не более 3 букв",
        (v) =>
          (v && v.toString().replace(/[a-zA-Zа-яА-Я]/g, "").length > 0) ||
          "Необходимо ввести номер (цифру)",
      ],
    };
  },

  mounted() {
    if (
      this.selectedTreeNode &&
      this.selectedTreeNode.parent &&
      this.selectedTreeNode.isParentCategory
    ) {
      this.object.code = this.selectedTreeNode.parent.parent.code;
    } else {
      this.object.code = this.selectedTreeNode.code;
    }
    this.onCategoryChange(this.category);
  },

  computed: {
    ...mapGetters(["selectedTreeNode", "sortBy", "sortDesc"]),

    code() {
      let node_parent_code = this.selectedTreeNode.isParentCategory
        ? this.selectedTreeNode.parent.code
        : this.selectedTreeNode.code;

      return this.object.code
        ? this.object.code + "-" + this.object.category.shortName
        : node_parent_code + "-" + this.object.category.shortName;
    },
  },

  watch: {
    category: {
      handler(val) {
        this.onCategoryChange(val);
      },
      deep: true,
    },
  },

  methods: {
    ...mapMutations(["setSnack"]),

    onCategoryChange(category) {
      this.object.category = category;
      this.isBuilding = category.id === CategoryUtils.types.BUILDINGS;
      this.isPipelines = category.id === CategoryUtils.types.PIPELINES;
      this.isBaseCenter =
        category.id === CategoryUtils.types.GR ||
        category.id === CategoryUtils.types.DM ||
        category.id === CategoryUtils.types.KDM ||
        category.id === CategoryUtils.types.PGDM ||
        category.id === CategoryUtils.types.GGDM ||
        category.id === CategoryUtils.types.TSG;

      if (category.id && !this.isBuilding) {
        let treeNodeId = this.selectedTreeNode.isParentCategory
          ? this.selectedTreeNode.parentId
          : this.selectedTreeNode.id;

        this.axios
          .get(`/objectTree/count/${category.id}/${treeNodeId}`)
          .then((response) => {
            this.object.orderCode = response.data.data;
          })
          .catch((thrown) => {
            console.error(thrown.message);
          });
      }
    },

    /**
     * Сохранение объекта
     */
    save(onSaveCallback) {
      if (!this.$refs.form.validate()) {
        return;
      }

      let treeNodeId = this.selectedTreeNode.isParentCategory
        ? this.selectedTreeNode.parentId
        : this.selectedTreeNode.id;

      return this.axios
        .post("/objectTree/add", {
          parentId: treeNodeId,
          name: this.object.name,
          centerX: this.object.centerX,
          centerY: this.object.centerY,
          categoryId: this.object.category.id,
          code: this.code + this.object.orderCode,
        })
        .then((response) => {
          if (
            this.selectedTreeNode.categoryId == CategoryUtils.types.PROJECT ||
            this.selectedTreeNode.categoryId == CategoryUtils.types.BUILDINGS ||
            this.selectedTreeNode.categoryId == CategoryUtils.types.PICKETS
          ) {
            this.$store.dispatch("addNewNode", {
              newNode: response.data.data,
            });
          } else {
            let treeNode = response.data.data;
            if (treeNode.isParentCategory) {
              if (!this.selectedTreeNode.gtmObjectsCount) {
                this.selectedTreeNode.gtmObjectsCount = 0;
              }

              this.selectedTreeNode.oldGtmObjectsCount =
                this.selectedTreeNode.gtmObjectsCount;
              this.selectedTreeNode.gtmObjectsCount++;
            } else {
              this.$store.dispatch("addNewNode", {
                newNode: response.data.data,
              });
            }
          }
          if (
            this.selectedTreeNode.categoryId === CategoryUtils.types.BUILDINGS
          ) {
            this.openCreatedCard(this.selectedTreeNode.objectId);
          }
        })
        .then(() => {
          this.setSnack({
            message: "Объект успешно сохранен",
            color: "success",
          });
          onSaveCallback();
        })
        .catch((thrown) => {
          console.error(thrown.response);
          this.setSnack({
            message:
              thrown.response && thrown.response.data.message
                ? thrown.response.data.message
                : "Не удалось сохранить объект",
            color: "error",
          });
        });
    },

    openCreatedCard(objectId) {
      this.$router.push(
        RoutesUtils.routes.CARD +
          RoutesUtils.routes.BUILDING_CARD +
          objectId +
          "/" +
          RoutesUtils.routes.PROPERTIES
      );
    },

    /**
     * Сохранение трубопровода
     */
    savePipelines(onSaveCallback) {
      if (!this.$refs.form.validate()) {
        return;
      }

      const pipeline = {
        pipeline: {
          name: this.object.name,
          parentId: this.selectedTreeNode.id,
          categoryId: this.object.category.id,
          code: this.code + this.object.orderCode,
        },
        listSection: this.isSections
          ? this.$refs.addSectionsControl.pipelineSections
          : [],
        picketCount: this.isSections
          ? 0
          : this.$refs.addPicketsControl.picketCount,
      };

      return this.axios
        .post("/objectTree/pipeline", pipeline)
        .then((response) => {
          let treeNode = response.data.data;
          this.$store.dispatch("addNewNode", {
            newNode: treeNode,
          });
          TreeUtils.sortItemsByParentCategoryId(
            this.selectedTreeNode,
            treeNode.parent.categoryId,
            this.sortBy,
            this.sortDesc
          );
        })
        .then(() => {
          this.setSnack({
            message: "Объект успешно сохранен",
            color: "success",
          });
          onSaveCallback();
        })
        .catch((thrown) => {
          console.error(thrown.response);
          this.setSnack({
            message:
              thrown.response && thrown.response.data.message
                ? thrown.response.data.message
                : "Не удалось сохранить объект",
            color: "error",
          });
        });
    },
  },
};
</script>
Пример, как это правильно работает:
Деформационная марка, ДМ (DeformationMarkProperties.vue), часть фронтенд:
<template>
  <v-container ma-0 pa-0 fluid>
    <v-form ref="form" v-model="valid" flat class="pa-8" lazy-validation>
      <v-row class="pb-3">
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiTextField
            maxlength="255"
            showTooltipLabel
            :disabled="disabled"
            label="Наименование"
            v-model="treeNode.name"
            :rules="[rules.required()]"
          />
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <v-tooltip top>
            <span> Конструктивные особенности </span>
            <template v-slot:activator="{ on, attrs }">
              <div v-on="on" v-bind="attrs">
                <NipiTextField
                  label="Конструктивные особенности"
                  :value="dmProperties.constructionFeatures"
                  hint="Пример: Резьба металлическая"
                  :disabled="disabled"
                />
              </div>
            </template>
          </v-tooltip>
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiTextField
            showTooltipLabel
            :disabled="true"
            label="Дата первого измерения"
            :value="dateFormatting(dmProperties.firstMeasurementDate)"
          />
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiTextField
            :disabled="true"
            showTooltipLabel
            defaultUnitMeasurement="м"
            label="Значение первого измерения"
            :hint="`Число в диапазоне [0;+\u221E]`"
            v-model="dmProperties.firstMeasurementValue"
            :rules="[rules.positiveNumber(), rules.numberDecimal(4)]"
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
        :categoryName="'Сведения о последнем измерении'"
      />
      <v-row class="pl-1">
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiTextField
            :disabled="true"
            showTooltipLabel
            label="Дата последнего измерения"
            :value="dateFormatting(dmProperties.lastMeasurementDate)"
          />
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <NipiTextField
            :disabled="true"
            showTooltipLabel
            defaultUnitMeasurement="м"
            label="Значение последнего измерения"
            :hint="`Число в диапазоне [0;+\u221E]`"
            v-model="dmProperties.lastMeasurementValue"
            :rules="[rules.positiveNumber(), rules.numberDecimal(4)]"
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
                  :value="dmProperties.visualInspection.dateInspection"
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
                    dmProperties.visualInspection &&
                    dmProperties.visualInspection.gtmElementState
                      ? dmProperties.visualInspection.gtmElementState.name
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

      <NipiCategoriesDivider
        class="my-8 divider-container"
        :md="md"
        :xs="xs"
        :sm="sm"
        :lg="lg"
        :xl="xl"
        :categoryName="'Координаты ДМ'"
      />
      <v-row class="pl-1">
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <v-tooltip bottom>
            <span> X координата ДМ{{ getCaption() }} </span>
            <template v-slot:activator="{ on, attrs }">
              <div
                v-on="on"
                v-bind="attrs"
                :class="{ readonly: dmProperties.hasGeometry }"
              >
                <NipiTextField
                  type="number"
                  label="X координата ДМ"
                  hint="Число в диапазоне [-∞; +∞]"
                  v-model="dmProperties.centerY"
                  :disabled="disabled || dmProperties.hasGeometry"
                />
              </div>
            </template>
          </v-tooltip>
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <v-tooltip bottom>
            <span> Y координата ДМ{{ getCaption() }} </span>
            <template v-slot:activator="{ on, attrs }">
              <div
                v-on="on"
                v-bind="attrs"
                :class="{ readonly: dmProperties.hasGeometry }"
              >
                <NipiTextField
                  type="number"
                  label="Y координата ДМ"
                  hint="Число в диапазоне [-∞; +∞]"
                  v-model="dmProperties.centerX"
                  :disabled="disabled || dmProperties.hasGeometry"
                />
              </div>
            </template>
          </v-tooltip>
        </v-col>
      </v-row>
    </v-form>
  </v-container>
</template>
<script>
import CategoryUtils from "@/assets/js/utils/categoryUtils";
import validationRules from "@/assets/js/utils/validationRulesUtils";
import NipiTextField from "../../NipiComponents/NipiTextField.vue";

export default {
  props: {
    // Отключен ли режим редактирования, по умолчанию отключен
    disabled: {
      type: Boolean,
      default: true,
    },
    // Данные для заполнения полей, в data должно быть 2 поля treeNode и dmProperties,
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
      dmProperties: {
        centerX: null,
        centerY: null,
        hasGeometry: false,
        constructionFeatures: null,
        firstMeasurementDate: null,
        firstMeasurementValue: null,
        verticalDeviationNorm: null,
        visualInspection: {
          dateInspection: null,
          gtmElementState: { id: null, name: null },
        },
      },
      treeNode: { name: null },
      rules: validationRules,
      categories: CategoryUtils,
    };
  },
  mounted() {
    if (this.data) {
      this.treeNode = this.data.treeNode;
      this.dmProperties = this.data.properties;
    }
  },

  watch: {
    data(val) {
      this.treeNode = val.treeNode;
      this.dmProperties = val.properties;
    },
  },

  methods: {
    /**
     * Преобразование формата даты
     */
    dateFormatting(val) {
      return val ? new Date(val).toLocaleDateString() : null;
    },

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
     * Обратка изменения даты
     */
    dateChange(date) {
      this.dmProperties.firstMeasurementDate = date;
    },

    getCaption() {
      return this.dmProperties.hasGeometry
        ? ". Для редактирования геометрии перейдите на карту"
        : "";
    },
  },
};
</script>

<style scoped>
.readonly >>> .theme--light.v-input--is-disabled input {
  color: darkgray;
}

.subheader-card {
  color: black;
  font-size: 18px;
  border-bottom: 1px solid #888888;
}
</style>
Деформационная марка, ДМ, часть бэкенд:
/**
 * Деформационная марка (ДМ/КДМ/ПГДМ/ГГДМ)
 */
@Entity
@Table(name = "deformation_mark")
@Getter
@NoArgsConstructor
public class DeformationMark extends BaseId implements IHasMeasurements {
    /**
     * Значение первого измерения, м
     * TODO GK-1075 Неиспользуемое поле, удалить после обсуждений с аналитиком
     */
    @Deprecated
    @Column(name = "first_measurement_value")
    private Double firstMeasurementValue;

    /**
     * Дата первого измерения
     * TODO GK-1075 Неиспользуемое поле, удалить после обсуждений с аналитиком
     */
    @Deprecated
    @Column(name = "first_measurement_date")
    private Timestamp firstMeasurementDate;

    /**
     * Номер марки (ДМ/КДМ/ПГДМ/ГГДМ)
     */
    @Setter
    @Column(name = "number")
    private String number;

    /**
     * Норма отклонения по вертикали, мм.
     * Для КДМ/ПГДМ/ГГДМ
     */
    @Setter
    @Column(name = "vertical_deviation_norm")
    private Double verticalDeviationNorm;

    /**
     * Тип деформационной марки
     */
    @Setter
    @Column(name = "deformation_mark_type")
    private DeformationMarkType deformationMarkType;

    /**
     * X координата репера
     */
    @Setter
    @Column(name = "center_x")
    private Double centerX;

    /**
     * Y координата репера
     */
    @Setter
    @Column(name = "center_y")
    private Double centerY;

    /**
     *  Измерения ДМ/КДМ/ПГДМ/ГГДМ
     */
    @Setter
    @OneToMany(mappedBy = "deformationMark"
            , cascade = CascadeType.ALL
            , orphanRemoval = true
            , fetch = FetchType.LAZY)
    private List<DeformationMarkMeasurement> measurements;

    @Column(name = "is_checked")
    @Setter
    private boolean isChecked;


    /**
     * Конструктивные особенности ДМ/ПГДМ/ГГДМ
     * */
    @Setter
    @Column(name = "construction_features")
    private String constructionFeatures;

    public DeformationMark(Double firstMeasurementValue, Timestamp firstMeasurementDate, String number, DeformationMarkType deformationMarkType) {
        this.firstMeasurementValue = firstMeasurementValue;
        this.firstMeasurementDate = firstMeasurementDate;
        this.number = number;
        this.deformationMarkType = deformationMarkType;
    }

    public DeformationMark(Double firstMeasurementValue, Timestamp firstMeasurementDate, String number, DeformationMarkType deformationMarkType, Double verticalDeviationNorm) {
        this(firstMeasurementValue, firstMeasurementDate, number, deformationMarkType);
        this.verticalDeviationNorm = verticalDeviationNorm;
    }

    public DeformationMark(DeformationMark original) {
        this.firstMeasurementValue = original.getFirstMeasurementValue();
        this.firstMeasurementDate = original.getFirstMeasurementDate();
        this.number = original.getNumber();
        this.verticalDeviationNorm = original.getVerticalDeviationNorm();
        this.deformationMarkType = original.getDeformationMarkType();
        this.constructionFeatures = original.getConstructionFeatures();
    }

    public void updateFirstMeasurement(Double firstMeasurementValue, Timestamp firstMeasurementDate) {
        this.firstMeasurementValue = firstMeasurementValue;
        this.firstMeasurementDate = firstMeasurementDate;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        DeformationMark that = (DeformationMark) o;
        return Objects.equals(getId(), that.getId())
                && Objects.equals(firstMeasurementValue, that.firstMeasurementValue)
                && Objects.equals(firstMeasurementDate, that.firstMeasurementDate)
                && Objects.equals(number, that.number)
                && Objects.equals(verticalDeviationNorm, that.verticalDeviationNorm)
                && deformationMarkType == that.deformationMarkType
                && Objects.equals(centerX, that.centerX)
                && Objects.equals(centerY, that.centerY)
                && Objects.equals(measurements, that.measurements)
                && Objects.equals(constructionFeatures, that.constructionFeatures);
    }

    @Override
    public int hashCode() {
        return Objects.hash(getId() ,firstMeasurementValue, firstMeasurementDate, number, verticalDeviationNorm, deformationMarkType, centerX, centerY, measurements, constructionFeatures);
    }

    /**
     * Проверяет количество измерений у ДМ
     *
     * @param count Необходимое количество измерений
     * @return Да - в случае, если количество измерений больше либо равно необходимому,
     * в противном случае - нет.
     */
    @Override
    public boolean hasCountOfMeasurements(int count) {
        return this.getMeasurements().size() >= count;
    }
}
@Getter
@NoArgsConstructor
public class DeformationMarkPropertiesDto implements BaseHaveCenter {
    /**
     * Идентификатор объекта
     */
    @JsonProperty("id")
    private UUID id;

    /**
     * Значение первого измерения, м
     */
    @JsonProperty("firstMeasurementValue")
    @DescriptionField("Значение первого измерения, м")
    @Min(value = 0, message = "Значение должно быть положительным")
    @NumberDecimal(decimal = 4, message = "Количество знаков после запятой не должно превышать 4")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double firstMeasurementValue;

    /**
     * Дата первого измерения
     */
    @JsonProperty("firstMeasurementDate")
    @DescriptionField("Дата первого измерения")
    @BeforeDate(date = "31-12-2100", message = "Дата должна быть раньше 31-12-2100")
    @AfterDate(date = "01-01-1900", message = "Дата должна быть позже 01-01-1900")
    private LocalDate firstMeasurementDate;

    /**
     * Дата последнего измерения
     */
    @JsonProperty("lastMeasurementDate")
    @DescriptionField("Дата последнего измерения")
    @BeforeDate(date = "31-12-2100", message = "Дата должна быть раньше 31-12-2100")
    @AfterDate(date = "01-01-1900", message = "Дата должна быть позже 01-01-1900")
    private LocalDate lastMeasurementDate;

    /**
     * Значение последнего измерения, м
     */
    @JsonProperty("lastMeasurementValue")
    @DescriptionField("Значение последнего измерения, м")
    @Min(value = 0, message = "Значение должно быть положительным")
    @NumberDecimal(decimal = 4, message = "Количество знаков после запятой не должно превышать 4")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double lastMeasurementValue;

    /**
     * Норма отклонения по вертикали, мм.
     * Для КДМ/ПГДМ/ГГДМ
     */
    @JsonProperty("verticalDeviationNorm")
    @DescriptionField("Норма отклонения по вертикали, мм")
    @Min(value = 0, message = "Значение должно быть положительным")
    @NumberDecimal(decimal = 1, message = "Количество знаков после запятой не должно превышать 1")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double verticalDeviationNorm;

    /**
     * X координата ДМ
     */
    @JsonProperty("centerX")
    @DescriptionField("X координата ДМ")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double centerX;

    /**
     * Y координата ДМ
     */
    @JsonProperty("centerY")
    @DescriptionField("Y координата ДМ")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double centerY;

    /**
     * Наличие геометрии
     */
    @JsonProperty("hasGeometry")
    private Boolean hasGeometry;

    /**
     * Последний технический осмотр
     */
    @JsonProperty("visualInspection")
    private GtsVisualInspectionDto visualInspection;

    /**
     * Конструктивные особенности ДМ/ПГДМ/ГГДМ
     * */
    @JsonProperty("constructionFeatures")
    @DescriptionField("Конструктивные особенности ДМ/ПГДМ/ГГДМ")
    @Size(max = 1000, message = "Количество символов не должно превышать 1000")
    private String constructionFeatures;

    public DeformationMarkPropertiesDto(DeformationMark dm) {
        this.id = dm.getId();
        this.firstMeasurementDate = dm.getFirstMeasurementDate() != null
                ? dm.getFirstMeasurementDate().toLocalDateTime().toLocalDate()
                : null;
        this.firstMeasurementValue = dm.getFirstMeasurementValue();
        this.verticalDeviationNorm = dm.getVerticalDeviationNorm();
        this.constructionFeatures = dm.getConstructionFeatures();
    }

    public DeformationMarkPropertiesDto(DeformationMark dm, Boolean hasGeometry, GtsVisualInspectionDto visualInspection) {
        this(dm);

        this.hasGeometry = hasGeometry;
        // #GK-1172 координаты x и y поменяны местами
        if (hasGeometry) {
            this.centerX = dm.getCenterY();
            this.centerY = dm.getCenterX();
        }
        this.visualInspection = visualInspection;
    }

    public DeformationMarkPropertiesDto(DeformationMark dm, Boolean hasGeometry, DeformationMarkMeasurement dmMeasurement, GtsVisualInspectionDto visualInspection) {
        this(dm, hasGeometry, visualInspection);

        setLastMeasument(dmMeasurement);
        setFirstMeasument(dmMeasurement);
    }

    public DeformationMarkPropertiesDto(DeformationMark dm, GeometryObjectPointPojo coordinatePtDto, GtsVisualInspectionDto visualInspection) {
        this(dm);

        this.centerX = coordinatePtDto.x;
        this.centerY = coordinatePtDto.y;
        this.hasGeometry = true;
        this.visualInspection = visualInspection;
    }

    public DeformationMarkPropertiesDto(DeformationMark dm, GeometryObjectPointPojo coordinatePtDto, DeformationMarkMeasurement dmMeasurement, GtsVisualInspectionDto visualInspection) {
        this(dm, coordinatePtDto, visualInspection);

        setLastMeasument(dmMeasurement);
        setFirstMeasument(dmMeasurement);
    }

    /**
     * Присвоение последнего измерения
     *
     * @param dmMeasurement Значение последнего измерения
     */
    public void setLastMeasument(DeformationMarkMeasurement dmMeasurement) {
        if (dmMeasurement != null) {
            this.lastMeasurementDate = dmMeasurement.getVerificationDate().toLocalDateTime().toLocalDate();
            this.lastMeasurementValue = dmMeasurement.getMeasurementValue();
        }
    }

    public void setFirstMeasument(DeformationMarkMeasurement dmMeasurement) {
        if (dmMeasurement != null) {
            this.firstMeasurementDate = dmMeasurement.getDeformationMark().getMeasurements().
                    stream().
                    min(Comparator.comparing(BaseMeasurement::getVerificationDate)).
                    orElse(new DeformationMarkMeasurement()).
                    getVerificationDate().toLocalDateTime().toLocalDate();
            this.firstMeasurementValue = dmMeasurement.getDeformationMark().getMeasurements().
                    stream().
                    min(Comparator.comparing(BaseMeasurement::getVerificationDate)).
                    orElse(new DeformationMarkMeasurement()).
                    getMeasurementValue();
        }
    }
}
@Log4j2

public class DeformationMarkCreatorHandler extends BaseCreatorHandler {
    private final Class<DeformationMark> entityClass = DeformationMark.class;
    private final Class<DeformationMarkMeasurement> measurementClass = DeformationMarkMeasurement.class;
    private final Class<DeformationMarkOperation> operationClass = DeformationMarkOperation.class;
    private final Class<GtsVisualInspection> gtsVisualInspection = GtsVisualInspection.class;
    private final Class<AddObjectDto> saveDtoClass = AddObjectDto.class;
    private final Class<AddMultipleObjectsDto> saveAddMultipleObjectsDtoClass = AddMultipleObjectsDto.class;
    private final Class<CopyObjectDto> copyDtoClass = CopyObjectDto.class;
    HashMap<UUID, DeformationMarkType> deformationMarkTypes = new HashMap<>();

    public DeformationMarkCreatorHandler(Repositories repositories, TransactionalHandler transactionalHandler, AppServiceForObjectCreatorChain appServiceForObjectCreatorChain) {
        super(repositories, transactionalHandler, appServiceForObjectCreatorChain);

        deformationMarkTypes.put(Category.Constants.DM_ID, DeformationMarkType.DM);
        deformationMarkTypes.put(Category.Constants.PGDM_ID, DeformationMarkType.PGDM);
        deformationMarkTypes.put(Category.Constants.GGDM_ID, DeformationMarkType.GGDM);
        deformationMarkTypes.put(Category.Constants.KDM_ID, DeformationMarkType.KDM);
    }

    @Override
    protected boolean isCategoryOfObject(UUID categoryId) {
        return categoryId.equals(Category.Constants.DM_ID)
                || categoryId.equals(Category.Constants.PGDM_ID)
                || categoryId.equals(Category.Constants.GGDM_ID)
                || categoryId.equals(Category.Constants.KDM_ID);
    }

    @Override
    protected TreeNode save(Object dto) {
        return transactionalHandler.runInTransaction(() -> {
            AddObjectDto objectDto = castDto(dto, saveDtoClass);
            checkCodeDuplication(objectDto.getCode());

            DeformationMarkRepository chainRepository = (DeformationMarkRepository) repositories.getRepositoryFor(entityClass).get();
            DeformationMarkType type = deformationMarkTypes.get(objectDto.getCategoryId());

            DeformationMark dm = new DeformationMark(null, null, null, type);
            BaseHaveCenter bhc = (BaseHaveCenter) dto;
            dm.setCenterX(bhc.getCenterX());
            dm.setCenterY(bhc.getCenterY());

            DeformationMark object = chainRepository.save(dm);

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

            DeformationMarkRepository chainRepository = (DeformationMarkRepository) repositories.getRepositoryFor(entityClass).get();
            DeformationMarkType type = deformationMarkTypes.get(objectDto.getCategoryId());
            DeformationMark original = chainRepository.findById(objectDto.getCopiedId()).orElseThrow(
                    () -> new BusinessException("Не найден копируемый объект"));
            DeformationMark dm = new DeformationMark(original);
            BaseHaveCenter bhc = (BaseHaveCenter) dto;
            dm.setCenterX(bhc.getCenterX());
            dm.setCenterY(bhc.getCenterY());

            DeformationMark object = chainRepository.save(dm);

            // todo в save и copy сохранение TreeNode одинаковое во всех хендлерах,
            //  нужно перенести это в базовый клас
            //  а также возможно тут попробовать убрать получение категории из базы

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
            DeformationMarkRepository chainRepository =
                    (DeformationMarkRepository) repositories.getRepositoryFor(entityClass).get();
            DeformationMarkType type = deformationMarkTypes.get(dto.getCategoryId());

            List<DeformationMark> deformationMarksForSave = new ArrayList<>();
            List<TreeNode> treeNodesForSave = new ArrayList<>();
            for (int i = 0; i < dto.getCount(); i++) {
                deformationMarksForSave.add(
                        new DeformationMark(null, null, null, type)
                );
            }

            List<DeformationMark> savedObjects = chainRepository.saveAll(deformationMarksForSave);

            Long max = ObjectTreeHelper.getCountObjectsUnderTreeNode(treeNodeRepository,
                    dto.getCategoryId(),
                    dto.getParentId());
            TreeNode parentTreeNode = treeNodeRepository.getOne(dto.getParentId());
            Category category = categoryRepository.getOne(dto.getCategoryId());
            for (DeformationMark savedObject : savedObjects) {
                max++;
                treeNodesForSave.add(new TreeNode(parentTreeNode.getCode() + '-' + category.getShortName() + max,
                        category.getShortName().toUpperCase() + " (" + max + ")",
                        parentTreeNode,
                        null,
                        category,
                        savedObject.getId()));
            }

            return treeNodeRepository.saveAll(treeNodesForSave);
        });
    }

    @Override
    protected Object update(Object dto) {
        return transactionalHandler.runInTransaction(() -> {
            DeformationMarkCardDto dmCardDto = castDto(dto, DeformationMarkCardDto.class);

            DeformationMarkPropertiesDto propertiesDto = dmCardDto.getProperties();
            TreeNodeDto treeNodeDto = dmCardDto.getTreeNode();

            DeformationMarkRepository chainRepository = (DeformationMarkRepository) repositories.getRepositoryFor(entityClass).get();

            DeformationMark dm = chainRepository.findById(propertiesDto.getId())
                    .orElseThrow(() -> new BusinessException("Не удалось найти объект"));

            dm.updateFirstMeasurement(
                    propertiesDto.getFirstMeasurementValue(),
                    propertiesDto.getFirstMeasurementDate() != null
                            ? Timestamp.valueOf(propertiesDto.getFirstMeasurementDate().atStartOfDay())
                            : null
            );
            dm.setVerticalDeviationNorm(propertiesDto.getVerticalDeviationNorm());

            dm.setCenterX(propertiesDto.getCenterX());
            dm.setCenterY(propertiesDto.getCenterY());

            dm.setConstructionFeatures(propertiesDto.getConstructionFeatures());

            TreeNode treeNode = treeNodeRepository.findById(treeNodeDto.getId())
                    .orElseThrow(() -> new BusinessException("Не удалось найти узел дерева объектов"));
            treeNode.setName(treeNodeDto.getName());

            TreeNode savedTreeNode = treeNodeRepository.save(treeNode);
            DeformationMark savedDm = chainRepository.save(dm);
            GtsVisualInspectionDto gtsVisualInspectionDto =
                    this.appServiceForObjectCreatorChain.getGtsVisualInspectionService().getLastVisualInspection(dmCardDto.getTreeNode().getObjectId());

            if (treeNode.getGeometryObject() == null) {
                return new DeformationMarkCardDto(new TreeNodeDto(savedTreeNode), new DeformationMarkPropertiesDto(savedDm, false, gtsVisualInspectionDto));
            } else {
                GeometryObjectPointPojo coordinatePtDto = new GeometryObjectPointPojo(dm.getCenterX(), dm.getCenterY());
                return new DeformationMarkCardDto(new TreeNodeDto(treeNode), new DeformationMarkPropertiesDto(dm, coordinatePtDto, gtsVisualInspectionDto));
            }
        });
    }

    @Override
    protected TreeNodeDto delete(UUID objectId) {
        return transactionalHandler.runInTransaction(() -> {
            DeformationMarkRepository chainRepository = (DeformationMarkRepository) repositories.getRepositoryFor(entityClass).get();
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
            GeoserverService geoserverService = this.appServiceForObjectCreatorChain.getGeoserverService();
            DeformationMarkRepository chainRepository = (DeformationMarkRepository) repositories.getRepositoryFor(entityClass).get();
            DeformationMarkMeasurementRepository dmMeasurementRepository =
                    (DeformationMarkMeasurementRepository) repositories.getRepositoryFor(measurementClass).get();
            BuildingRepository buildingRepository = (BuildingRepository) repositories.getRepositoryFor(Building.class).get();
            PicketRepository picketRepository = (PicketRepository) repositories.getRepositoryFor(Picket.class).get();
            DictVerticalElevationTypeRepository dictVerticalElevationTypeRepository = (DictVerticalElevationTypeRepository) repositories.getRepositoryFor(DictVerticalElevationType.class).get();

            List<OperationBlankField> blankFields = blank.getFields();

            for (OperationDto operation : dto.getOperations()) {
                DeformationMark obj = chainRepository.getOne(operation.getObject().getObjectId());
                obj.setDeformationMarkType(deformationMarkTypes.get(blank.getCategory().getId()));

                fillObjectPropertiesFromOperationBlankFields(
                        operation,
                        blankFields,
                        chainRepository,
                        obj);

                chainRepository.save(obj);

                TreeNode treeNode = treeNodeRepository.getByObjectId(operation.getObject().getObjectId());
                Optional<DeformationMark> dmOptional = chainRepository.findById(obj.getId());
                if (dmOptional.isPresent()) {
                    GtsVisualInspectionDto gtsVisualInspectionDto =
                            this.appServiceForObjectCreatorChain.getGtsVisualInspectionService().getLastVisualInspection(operation.getObject().getObjectId());
                    DeformationMarkPropertiesDto dmPropertiesDto = new DeformationMarkPropertiesDto(dmOptional.get(), true, gtsVisualInspectionDto);
                    try {
                        geoserverService.updateObjectGeometry(treeNode, dmPropertiesDto);
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }

                DeformationMarkHelper.updateCalculatedFieldsInAllMeasurements(obj.getFirstMeasurementValue(),
                        obj.getId(),
                        treeNode.getParent(),
                        dmMeasurementRepository,
                        buildingRepository,
                        picketRepository,
                        dictVerticalElevationTypeRepository);

            }
            return null;
        });
    }

    @Override
    protected ImportableDto importMeasurement(ImportableDto dto, OperationBlank blank, String userSid) {
        return transactionalHandler.runInTransaction(() -> {
            DeformationMarkRepository chainRepository = (DeformationMarkRepository) repositories.getRepositoryFor(entityClass).get();
            BuildingRepository buildingRepository = (BuildingRepository) repositories.getRepositoryFor(Building.class).get();
            PicketRepository picketRepository = (PicketRepository) repositories.getRepositoryFor(Picket.class).get();
            DictVerticalElevationTypeRepository dictVerticalElevationTypeRepository = (DictVerticalElevationTypeRepository) repositories.getRepositoryFor(DictVerticalElevationType.class).get();

            DeformationMarkMeasurementRepository dmMeasurementRepository
                    = (DeformationMarkMeasurementRepository) repositories.getRepositoryFor(measurementClass).get();
            StorageRepository storageRepository = (StorageRepository) repositories.getRepositoryFor(Storage.class).get();
            List<OperationBlankField> blankFields = blank.getFields();
            Storage storage = storageRepository.getOne(dto.getId());
            Map<DeformationMark, List<DeformationMarkMeasurement>> measurementsByDeformationMark = new HashMap<>();

            for (OperationDto operation : dto.getOperations()) {
                DeformationMark obj = chainRepository.findById(operation.getObject().getObjectId()).orElseThrow(() ->
                        new BusinessException(
                                "Не удалось найти деформационную марку по id: " +
                                operation.getObject().getObjectId()
                        ));
                DeformationMarkMeasurement measurement = new DeformationMarkMeasurement();
                measurement.setLoadDate(new Timestamp(new Date().getTime()));
                measurement.setDeformationMark(obj);
                measurement.setStorage(storage);
                measurement.setUserSid(userSid);
                measurement.setVerificationDate(new Timestamp(operation.getDate().getTime()));

                for (OperationBlankField blankField : blankFields) {
                    PropertyAccessor myAccessor = PropertyAccessorFactory.forBeanPropertyAccess(measurement);
                    Object value = operation.getObject().getProperties().stream().filter(s -> s.getFieldId().equals(blankField.getId())).findFirst().get().getValue();
                    setValueToProperty(value, blankField, myAccessor);
                }

                obj.getMeasurements().add(measurement);

                //Обновление последующего измерения
                DeformationMarkMeasurement dmMeasurementAfterNew = dmMeasurementRepository.
                        findFirstByVerificationDateAfterAndDeformationMarkIdOrderByVerificationDateAsc(measurement.getVerificationDate(), obj.getId());
                if (dmMeasurementAfterNew != null) {
                    dmMeasurementAfterNew.updateVerticalDeviation(
                            measurement.getMeasurementValue(),
                            dmMeasurementAfterNew.getMeasurementValue()
                    );
                    dmMeasurementRepository.save(dmMeasurementAfterNew);
                }

                if (measurementsByDeformationMark.containsKey(obj)) {
                    measurementsByDeformationMark.get(obj).add(measurement);
                } else {
                    List<DeformationMarkMeasurement> measurements = new ArrayList<>();
                    measurements.add(measurement);
                    measurementsByDeformationMark.put(obj, measurements);
                }
            }

            for (Map.Entry<DeformationMark, List<DeformationMarkMeasurement>> entry : measurementsByDeformationMark.entrySet()) {
                DeformationMark deformationMark = entry.getKey();
                DeformationMarkMeasurement deformationMarkMeasurement = entry.getValue().stream().min(Comparator.comparing(DeformationMarkMeasurement::getVerificationDate)).get();
                deformationMark.updateFirstMeasurement(
                        deformationMarkMeasurement.getMeasurementValue(),
                        deformationMarkMeasurement.getVerificationDate()
                );
                chainRepository.save(deformationMark);

                entry.getValue().forEach(measurement -> {
                    DeformationMarkMeasurement dmMeasurementBeforeNew = dmMeasurementRepository
                            .findFirstByVerificationDateBeforeAndDeformationMarkIdOrderByVerificationDateDesc(
                                    measurement.getVerificationDate(), deformationMark.getId());
                    Double lastMeasurementValue = dmMeasurementBeforeNew != null ?
                            dmMeasurementBeforeNew.getMeasurementValue() : deformationMark.getFirstMeasurementValue();

                    measurement.updateVerticalDeviation(lastMeasurementValue, measurement.getMeasurementValue());
                    Double totalVerticalDeviation = DeformationMarkHelper.calculateTotalVerticalDeviation(deformationMark.getFirstMeasurementValue(), measurement.getMeasurementValue());
                    measurement.setTotalVerticalDeviation(totalVerticalDeviation);

                    TreeNode constructionTreeNode = treeNodeRepository.getByObjectId(deformationMark.getId()).getParent();
                    ConstructionElevationDto constructionElevationDto = DeformationMarkHelper.getConstructionElevationDto(constructionTreeNode, buildingRepository, picketRepository);

                    UUID verticalElevationTypeId = DeformationMarkHelper.calculateVerticalElevationTypeId(
                            constructionElevationDto.getMaxLiftMM(),
                            constructionElevationDto.getMaxDraftMM(),
                            totalVerticalDeviation,
                            totalVerticalDeviation == null ? null : Math.abs(totalVerticalDeviation));

                    measurement.setVerticalElevationType(verticalElevationTypeId != null ?
                            dictVerticalElevationTypeRepository.getOne(verticalElevationTypeId) : null);

                    dmMeasurementRepository.save(measurement);
                });
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
        DeformationMarkOperationRepository chainRepository = (DeformationMarkOperationRepository) repositories.getRepositoryFor(operationClass).get();

        return chainRepository.findAll(
                (Specification<DeformationMarkOperation>) spec,
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
                DeformationMarkMeasurementRepository chainRepository = (DeformationMarkMeasurementRepository) repositories.getRepositoryFor(measurementClass).get();
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
        return ((DeformationMarkMeasurementRepository) repositories.getRepositoryFor(measurementClass).get())
                .existsByDeformationMarkIdAndVerificationDate(objectId, verificationDate);
    }

    /**
     * Удаление списка объектов по идентификатору объекта
     *
     * @param listDeletedObjects - список объектов
     * @return список идентификаторов удаленных измерений
     */
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
                DeformationMarkRepository chainRepository = (DeformationMarkRepository) repositories.getRepositoryFor(entityClass).get();
                chainRepository.deleteByIdIn(listObjectsId);
                treeNodeRepository.deleteByIdIn(listTreeNodeId);
                bindingRepository.deleteAllByBindedIn(listTreeNodeId);
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
Теперь перейдём к моему комноненту, для которого я пытаюсь реализовать аткой же функционал:
Термостабилизатор грунта, ТСГ (GroundThermalStabilizerProperties.vue), часть фронтенд:
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
                  :value="tsgProperties.constructionFeatures"
                  hint="Пример: Резьба металлическая"
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

      <NipiCategoriesDivider
        class="my-8 divider-container"
        :md="md"
        :xs="xs"
        :sm="sm"
        :lg="lg"
        :xl="xl"
        :categoryName="'Координаты ТСГ'"
      />
      <v-row class="pl-1">
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <v-tooltip bottom>
            <span> X координата ТСГ{{ getCaption() }} </span>
            <template v-slot:activator="{ on, attrs }">
              <div
                v-on="on"
                v-bind="attrs"
                :class="{ readonly: tsgProperties.hasGeometry }"
              >
                <NipiTextField
                  type="number"
                  label="X координата ТСГ"
                  hint="Число в диапазоне [-∞; +∞]"
                  v-model="tsgProperties.centerY"
                  :disabled="disabled || tsgProperties.hasGeometry"
                />
              </div>
            </template>
          </v-tooltip>
        </v-col>
        <v-col class="py-2" :md="md" :xs="xs" :sm="sm" :lg="lg" :xl="xl">
          <v-tooltip bottom>
            <span> Y координата ТСГ{{ getCaption() }} </span>
            <template v-slot:activator="{ on, attrs }">
              <div
                v-on="on"
                v-bind="attrs"
                :class="{ readonly: tsgProperties.hasGeometry }"
              >
                <NipiTextField
                  type="number"
                  label="Y координата ТСГ"
                  hint="Число в диапазоне [-∞; +∞]"
                  v-model="tsgProperties.centerX"
                  :disabled="disabled || tsgProperties.hasGeometry"
                />
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
        centerX: null,
        centerY: null,
        hasGeometry: false,
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

    getCaption() {
      return this.tsgProperties.hasGeometry
        ? ". Для редактирования геометрии перейдите на карту"
        : "";
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
Термостабилизатор грунта, ТСГ, часть бэкенд:
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

    /**
     * X координата ТСГ
     */
    @Column(name = "center_x")
    private Double centerX;

    /**
     * Y координата ТСГ
     */
    @Column(name = "center_y")
    private Double centerY;

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
        this.setCenterX(original.getCenterX());
        this.setCenterY(original.getCenterY());
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

    /**
     * X координата ТСГ
     */
    @JsonProperty("centerX")
    @DescriptionField("X координата ТСГ")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double centerX;

    /**
     * Y оордината ТСГ
     */
    @JsonProperty("centerY")
    @DescriptionField("Y оордината ТСГ")
    @JsonDeserialize(using = DoubleFixLocaleDeserializer.class)
    private Double centerY;

    /**
     * Наличие геометрии
     */
    @JsonProperty("hasGeometry")
    private Boolean hasGeometry;

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
        this.centerX =  tsg.getCenterX();
        this.centerY =  tsg.getCenterY();

        this.visualInspection = visualInspection;
    }

    public GroundThermalStabilizerPropertiesDto(GroundThermalStabilizer tsg) {
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
        this.upperPartLength = tsg.getUpperPartLength();
        this.evaporatorPipeDiameter = tsg.getEvaporatorPipeDiameter();
        this.constructionFeatures = tsg.getConstructionFeatures();
    }

    public GroundThermalStabilizerPropertiesDto(GroundThermalStabilizer tsg, Boolean hasGeometry, GtsVisualInspectionDto visualInspection) {
        this(tsg);

        this.hasGeometry = hasGeometry;
        if (hasGeometry) {
            this.centerX = tsg.getCenterY();
            this.centerY = tsg.getCenterX();
        }
        this.visualInspection = visualInspection;
    }

    public GroundThermalStabilizerPropertiesDto(GroundThermalStabilizer tsg, GeometryObjectPointPojo coordinatePtDto, GtsVisualInspectionDto visualInspection) {
        this(tsg);

        this.centerX = coordinatePtDto.x;
        this.centerY = coordinatePtDto.y;
        this.hasGeometry = true;
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
            BaseHaveCenter bhc =  (BaseHaveCenter) dto;
            object.setCenterX(bhc.getCenterX());
            object.setCenterY(bhc.getCenterY());

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
            BaseHaveCenter bhc =  (BaseHaveCenter) dto;
            object.setCenterX(bhc.getCenterX());
            object.setCenterY(bhc.getCenterY());

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
            tsg.setCenterX(propertiesDto.getCenterX());
            tsg.setCenterY(propertiesDto.getCenterY());

            TreeNode treeNode = treeNodeRepository.findById(treeNodeDto.getId())
                    .orElseThrow(() -> new BusinessException("Не удалось найти узел дерева объектов"));
            treeNode.setName(treeNodeDto.getName());

            TreeNode savedTreeNode = treeNodeRepository.save(treeNode);
            GroundThermalStabilizer savedTsg = chainRepository.save(tsg);
            GtsVisualInspectionDto gtsVisualInspectionDto =
                    this.appServiceForObjectCreatorChain.getGtsVisualInspectionService().getLastVisualInspection(tsgCardDto.getTreeNode().getObjectId());

            if (treeNode.getGeometryObject() == null) {
                return new GroundThermalStabilizerCardDto(new TreeNodeDto(savedTreeNode), new GroundThermalStabilizerPropertiesDto(savedTsg, gtsVisualInspectionDto));
            } else {
                GeometryObjectPointPojo coordinatePtDto = new GeometryObjectPointPojo(tsg.getCenterX(), tsg.getCenterY());
                return new GroundThermalStabilizerCardDto(new TreeNodeDto(treeNode), new GroundThermalStabilizerPropertiesDto(tsg, coordinatePtDto, gtsVisualInspectionDto));
            }
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
