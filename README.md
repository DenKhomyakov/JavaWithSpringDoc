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
Привет! Мне нужно, чтобы при изменении размеров окна наша ссылка "Скачать результаты прогноза" и икона изменялись вместе с ним, потому что сейчас оно выходит за его пределы, выезжает
ForecastResults.vue:
<template>

  <v-container>

    <!-- <div class="position-container"> -->

      <div>

        <DownArrowIcon size="16"/>

        <v-btn
          @click="downloadForecastResults"
          class="download-doc-btn"
          small
        >
          Скачать результаты прогноза
        </v-btn>

      </div>

    <!-- </div> -->

  </v-container>

</template>

<script>

import scssVar from "@/styles/exportVarToJs.scss";
import { mapMutations } from "vuex";
import DownArrowIcon from "@/components/Icons/DownArrowIcon.vue";
import FileServiceUtil from "@/assets/js/utils/fileServiceUtil";
import { compose } from "ol/transform";

export default {

  components: { DownArrowIcon },
  props: ["forecastId"],

  data() {

    return {

      toolbarHeight: scssVar.toolbarHeight,

      perPageItems: [10, 20, 40, 80],
      itemsPerPage: 10,
      countMarker: 0,
      items: [],

      reliabilityIndicators: {

        forecastResults: [],
        treeNodeResultTracer: null,

      },
    };
  },

  mounted() {
    this.getForecastResults();
  },

  methods: {
    ...mapMutations(["setSnack"]),

    getForecastResults() {

      this.reliabilityIndicators.forecastResults = [];

      this.axios
        .get(`/solv/forecastsolver/forecastResults/${this.forecastId}`)
        .then((response) => {

          if (!response.data.data) {
            return;
          }

          this.reliabilityIndicators.forecastResults = response.data.data;
        })
        .catch((thrown) => {

          console.error(thrown.response);

          if (thrown.response && thrown.response.data.message) {

            this.forecastResults.push({
              error: thrown.response.data.message,
            });
          }
        });
    },

    downloadForecastResults() {
      console.log("sdfopsfdkopsdfk")
      this.axios
        .get(

          `/solv/forecastsolver/initiateDownloadAndGetForecastResults/${this.forecastId}`,

          {
            responseType: "blob",
          }

        )
        .then((response) => {

          this.setSnack({

            message: "Файлы результатов прогноза успешно скачаны",
            color: "success",

          });

          let fileName = FileServiceUtil.getFileNameFromResponse(
            response.headers
          );

          FileServiceUtil.downloadFile(fileName, response.data);

        })
        .catch((thrown) => {

          console.error(thrown.message);

          this.setSnack({

            message: "Не удалось скачать файлы результатов прогноза",
            color: "error",
          });
        });
    },
  },
};

</script>

<style>

.position-container {
  display: flex;
  justify-content: center;
  align-items: center;
}

.download-doc-btn {
  border: none !important;
  background: none !important;
  box-shadow: none;
  text-decoration: underline !important;
  transition: color 0.3s, text-decoration 0.3s;
}

.position-bnt-and-icon {
  max-width: 50%;
  margin-right: -1100px;
  margin-top: -380px;
  display: flex;
  align-items: center;
}

.download-doc-btn:focus,
.download-doc-btn:hover {
  border: none !important;
  background: none !important;
  box-shadow: none;
  outline: none;
  color: rgb(86, 153, 209);
}

.download-doc-btn::before {
  display: none;
}

.download-doc-btn:active::before {
  display: none;
}

</style>
ForecastDetailDialog.vue:
<template>
  <v-dialog v-model="isForecastDetailDialogVisible" width="80%">
    <v-card>
      <v-container
        v-if="loader.isLoading"
        :class="loader.isLoading ? 'loader_active' : ''"
      >
        <NipiLoader loaderSize="100" :indeterminate="loader.indeterminate" />
      </v-container>

      <div v-else>
        <v-card-title v-if="forecastDetailsInfo">
          Прогноз для {{ selectedTreeNode.code }} от
          {{ new Date(forecastDetailsInfo.createDate).toLocaleString() }}
          <v-spacer />
          <v-btn icon @click="isForecastDetailDialogVisible = false">
            <v-icon v-text="'$mdiClose'" />
          </v-btn>
        </v-card-title>

        <v-card-title v-else>
          Просмотр невозможен: {{ errorMessage }}
        </v-card-title>

        <v-card-text v-if="forecastDetailsInfo">
          <div v-if="forecastDetailsInfo.moments">
            <div>
              За период с:
              {{
                new Date(forecastDetailsInfo.moments[0]).toLocaleDateString()
              }}
              по
              <!-- todo переписать -->
              {{
                forecastDetailsInfo.moments.length > 2
                  ? new Date(
                      forecastDetailsInfo.moments[3]
                    ).toLocaleDateString()
                  : new Date(
                      forecastDetailsInfo.moments[1]
                    ).toLocaleDateString()
              }}
              <div style="display: inline;">
                <ForecastResults :forecastId="forecastDetailsInfo.forecastId"/>
              </div>
            </div>

            <div class="date-list">
              {{
                forecastDetailsInfo.dateTSMeasurementList.length < 2
                  ? "Измерение на дату: "
                  : "Измерения на даты: "
              }}
              <div
                v-for="(
                  date, index
                ) in forecastDetailsInfo.dateTSMeasurementList"
                :key="index"
                class=""
              >
                {{ new Date(date).toLocaleDateString() }};
              </div>
            </div>

            <p class="mb-0">
              Параметры расчета: {{ forecastDetailsInfo.description }};
            </p>

            <p class="mb-0">
              Учет тренда глобального потепления:
              {{ forecastDetailsInfo.isUseGlobalWarmingTrend ? "Да" : "Нет" }};
            </p>

            <p class="mb-0">
              Учет талой части:
              {{ forecastDetailsInfo.isUseThawedPart ? "Да" : "Нет" }};
            </p>

            <p class="mb-0">
              Учет автоматической адаптации климатической модели:
              {{ forecastDetailsInfo.isUseClimateAdaptation ? "Да" : "Нет" }};
            </p>

            <p class="mb-0">
              Версия расчетного сервера:
              {{ forecastDetailsInfo.solvServerVersion }}
            </p>

            <p
              v-if="reliabilityIndicators && reliabilityIndicators.pileGroup"
              class="mb-0"
            >
              Группа свай:
              {{ reliabilityIndicators.pileGroup.pileGroupName }}
            </p>

            <p class="mb-0">
              Режим расчета:
              <span
                v-if="
                  forecastDetailsInfo.solverMode ===
                  solverModeUtils.modes.FORECAST
                "
              >
                Моделирование до конца срока эксплуатации
              </span>
              <span
                v-if="
                  forecastDetailsInfo.solverMode === solverModeUtils.modes.FCUF
                "
              >
                Моделирование до отказа несущей способности
              </span>
            </p>

            <div>
              <ForecastResults :forecastId="forecastDetailsInfo.forecastId"/>
            </div>

            <template
              v-if="
                forecastDetailsInfo.solverMode === solverModeUtils.modes.FCUF
              "
            >
              <!-- TODO переписать нормально, срочно! -->
              <p class="mb-0" v-if="forecastDetailsInfo.isForecastFailed">
                Дата отказа:
                {{
                  new Date(forecastDetailsInfo.moments[1]).toLocaleDateString()
                }}
              </p>
              <p class="mb-0" v-else>
                Устойчивость свайных оснований обеспечена на весь срок
                эксплуатации
              </p>
            </template>
          </div>

          <div v-else>
            <v-row>
              За период с:
              {{
                new Date(
                  forecastDetailsInfo.facilityCommissioningDate
                ).toLocaleDateString()
              }}
              по
              {{
                forecastDetailsInfo.structuresServiceLifeEnd
                  ? new Date(
                      forecastDetailsInfo.structuresServiceLifeEnd
                    ).toLocaleDateString()
                  : ""
              }}
              (даты "с:", "по" могут измениться по завершении расчета),
              измерение на дату:
              {{
                new Date(
                  forecastDetailsInfo.dateTSMeasurementList
                ).toLocaleDateString()
              }},
              <p class="mb-0">
                параметры расчета: {{ forecastDetailsInfo.description }},
              </p>
              <p class="mb-0">
                учет тренда глобального потепления:
                {{
                  forecastDetailsInfo.isUseGlobalWarmingTrend ? "Да" : "Нет"
                }};
              </p>
              <p class="mb-0">
                учет талой части:
                {{ forecastDetailsInfo.isUseThawedPart ? "Да" : "Нет" }};
              </p>
              <p class="mb-0">
                учет автоматической адаптации климатической модели:
                {{ forecastDetailsInfo.isUseClimateAdaptation ? "Да" : "Нет" }};
              </p>
              <p class="mb-0">
                версия расчетного сервера:
                {{ forecastDetailsInfo.solvServerVersion }}
              </p>
              <p
                v-if="reliabilityIndicators && reliabilityIndicators.pileGroup"
                class="mb-0"
              >
                Группа свай:
                {{ reliabilityIndicators.pileGroup.pileGroupName }}
              </p>
            </v-row>

            <v-row align="center" justify="center">
              <div>
                <p
                  v-if="
                    forecast.codeStatus !== 'Aborted' &&
                    forecast.codeStatus !== 'Finished'
                  "
                >
                  <b>Просмотр невозможен, расчет еще не завершен</b>
                </p>
              </div>
            </v-row>
          </div>
        </v-card-text>

        <v-card-text
          v-if="
            forecast.codeStatus === 'Aborted' ||
            forecast.codeStatus === 'Finished' ||
            forecastDetailsInfo.resultStatus === 'RESULT_READY'
          "
        >
          <PileLoad
            :forecastId="forecastDetailsInfo.forecastId"
            :forecastSolverMode="forecastDetailsInfo.solverMode"
            @openLogTraceDialog="openLogTraceTreeDialog"
            @indicatorsLoaded="reliabilityIndicators = $event"
          />

          <PileSide :forecastId="forecastDetailsInfo.forecastId" />

          <ErrorBannerLogTrace
            v-if="forecastDetailsInfo.resultStatus === 'RESULT_ERROR'"
            @openLogDialog="
              openLogTraceTreeDialog(forecastDetailsInfo.treeNodeResultTracer)
            "
            text="Расчет теплотехнического прогноза завершен с ошибкой"
          />
          <ForecastResultList
            v-else-if="forecastDetailsInfo.resultByDepth"
            :forecastResultDate="forecastDetailsInfo"
            :forecastSolverMode="forecastDetailsInfo.solverMode"
            @openLogTraceDialog="openLogTraceTreeDialog"
          />

          <IntermediateResults :forecastId="forecastDetailsInfo.forecastId" />

          <LogTraceTreeDialog
            v-if="logTraceDialog.visibleLogTraceDialog"
            :visible="logTraceDialog.visibleLogTraceDialog"
            :logTraceTree="logTraceDialog.logTraceTree"
            @closeLogTraceDialog="closeLogTraceDialog"
          />
        </v-card-text>

        <v-card-actions>
          <v-spacer />
          <v-btn text @click="isForecastDetailDialogVisible = false">
            Закрыть
          </v-btn>
        </v-card-actions>
      </div>
    </v-card>
  </v-dialog>
</template>

<script>
import SolverModeUtils from "@/assets/js/utils/solverModeUtils";
import ErrorBannerLogTrace from "@/components/Cards/SolveModule/LogTraceSolveServer/ErrorBannerLogTrace";
import LogTraceTreeDialog from "@/components/Cards/SolveModule/LogTraceSolveServer/LogTraceTreeDialog";
import IntermediateResults from "@/components/Cards/SolveModule/PileLoad/IntermediateResults";
import ForecastResults from "@/components/Cards/SolveModule/PileLoad/ForecastResults";
import PileLoad from "@/components/Cards/SolveModule/PileLoad/PileLoad";
import PileSide from "@/components/Cards/SolveModule/PileLoad/PileSide";
import ForecastResultList from "@/components/Dialogs/SolveDialogs/components/ForecastResultList";
import NipiLoader from "@/components/NipiComponents/NipiLoader";
import { mapGetters, mapMutations } from "vuex";

export default {
  name: "ForecastDetailDialog",
  components: {
    ErrorBannerLogTrace,
    LogTraceTreeDialog,
    NipiLoader,
    ForecastResultList,
    PileSide,
    PileLoad,
    IntermediateResults,
    ForecastResults,
  },
  props: {
    forecast: {
      type: Object,
      default: null,
    },
  },

  data() {
    return {
      solverModeUtils: SolverModeUtils,
      forecastDetailsInfo: null,
      reliabilityIndicators: null,
      loader: {
        isLoading: true,
        indeterminate: true,
      },
      logTraceDialog: {
        visibleLogTraceDialog: false,
        logTraceTree: {},
      },
      errorMessage: "",
    };
  },
  computed: {
    ...mapGetters(["selectedTreeNode"]),

    isForecastDetailDialogVisible: {
      get() {
        return this.$store.state.dialogs.isForecastDetailDialogVisible;
      },
      set(value) {
        this.$store.commit("updateIsForecastDetailDialogVisible", value);
      },
    },
  },

  watch: {},

  mounted() {
    this.getForecastResult();
  },

  methods: {
    ...mapMutations(["setSnack"]),

    /**
     * Получить карточки с результатом прогноза
     */
    getForecastResult() {
      this.loader.isLoading = true;
      let that = this;
      this.axios
        .get(`/solv/forecastsolver/result/${this.forecast.id}`)
        .then((response) => {
          this.forecastDetailsInfo = response.data.data;
        })
        .catch((thrown) => {
          that.loader.isLoading = false;
          console.error(thrown.response);
          that.errorMessage =
            thrown.response && thrown.response.data.message
              ? thrown.response.data.message
              : "Не удалось получить информацию о прогнозе";
          this.setSnack({
            message: that.errorMessage,
            color: "error",
          });
        })
        .finally(() => {
          this.loader.isLoading = false;
        });
    },

    /**
     * Открытие окна журнала расчета
     */
    openLogTraceTreeDialog(logTraceTree) {
      if (!logTraceTree) {
        this.setSnack({
          message: "Отсутствуют данные в журнале расчета",
          color: "warning",
        });
        return;
      }
      this.logTraceDialog.visibleLogTraceDialog = true;
      this.logTraceDialog.logTraceTree = logTraceTree;
    },

    /**
     * Закрытие окна журнала расчета
     */
    closeLogTraceDialog() {
      this.logTraceDialog.visibleLogTraceDialog = false;
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

.loader_active {
  height: 300px;
}

.date-list {
  display: flex;
  flex-wrap: wrap;
  gap: 0.25em;
}
</style>
