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
          headerTitle="Сводная таблица отклонений"
          @closeDialog="closeSummaryTableDialog()"
        />
      </v-card-title>

      <!-- Быстрые фильтры (Chip) -->
      <v-card-text class="pt-4 pb-2">
        <div class="d-flex align-center mb-2">
          <span class="text-caption text--secondary">
            Активные фильтры: {{ activeFilters.length }}
          </span>
          <v-btn 
            v-if="activeFilters.length > 0"
            small 
            text 
            @click="activeFilters = []"
            class="ml-2"
          >
            Сбросить все
          </v-btn>
        </div>
        <div class="filter-chips">
          <v-chip
            v-for="filter in quickFilters"
            :key="filter.value"
            class="ma-1"
            :color="activeFilters.includes(filter.value) ? 'primary' : 'default'"
            @click="toggleFilter(filter.value)"
          >
            {{ filter.text }}
          </v-chip>
        </div>
      </v-card-text>

      <!-- Основная таблица -->
      <v-data-table
        :headers="tableHeaders"
        :items="filteredStructures"
        :items-per-page="10"
        :loading="loading"
        :server-items-length="totalStructures"
        :options.sync="pagination"
        disable-sort
        class="table-gtm-elements pl-5 pr-6 v-data-table elevation-1"
      >
        <!-- Кастомный шаблон для иконок статусов -->
        <template v-slot:item.calculations="{ item }">
          <v-icon
            v-if="item.calculations !== null"
            :color="item.calculations ? 'success' : 'error'"
          >
            {{ item.calculations ? 'mdi-check' : 'mdi-close' }}
          </v-icon>
          <span v-else>-</span>
        </template>

        <template v-slot:item.dm="{ item }">
          <v-icon
            v-if="item.dm !== null"
            :color="item.dm ? 'success' : 'error'"
          >
            {{ item.dm ? 'mdi-check' : 'mdi-close' }}
          </v-icon>
          <span v-else>-</span>
        </template>

        <template v-slot:item.ts="{ item }">
          <v-icon
            v-if="item.ts !== null"
            :color="item.ts ? 'success' : 'error'"
          >
            {{ item.ts ? 'mdi-check' : 'mdi-close' }}
          </v-icon>
          <span v-else>-</span>
        </template>

        <!-- Шаблон для объекта с гиперссылкой -->
        <template v-slot:item.object="{ item }">
          <a
            href="#"
            class="object-link"
            @click.prevent="navigateToStructure(item)"
          >
            {{ item.object }}
          </a>
        </template>

        <!-- Состояние загрузки -->
        <template v-slot:loading>
          <v-row justify="center" align="center">
            <v-col cols="12" class="text-center">
              <v-progress-circular
                indeterminate
                color="primary"
              ></v-progress-circular>
              <div class="mt-2">Загрузка данных...</div>
            </v-col>
          </v-row>
        </template>

        <!-- Состояние пустой таблицы -->
        <template v-slot:no-data>
          <v-row justify="center" align="center" class="py-8">
            <v-col cols="12" class="text-center">
              <v-icon size="64" color="grey lighten-1">mdi-alert-circle-outline</v-icon>
              <div class="mt-2 text-h6">Нет сооружений с отклонениями</div>
              <div class="text-body-1 text--secondary">
                Все сооружения соответствуют требованиям или данные отсутствуют
              </div>
            </v-col>
          </v-row>
        </template>
      </v-data-table>

      <v-card-actions class="shadow-for-card-actions">
        <v-spacer />
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
import { mapMutations } from "vuex";

export default {
  name: "SummaryTableDialog",
  components: {
    DialogHeader,
  },

  data() {
    return {
      loading: false,
      structures: [], // Исходные данные с бэка
      activeFilters: [], // Активные фильтры
      pagination: {},
      totalStructures: 0,

      // Быстрые фильтры
      quickFilters: [
        { text: 'С отклонениями в расчетах', value: 'calculations_error' },
        { text: 'С отклонениями по ДМ', value: 'dm_error' },
        { text: 'С отклонениями по ТС', value: 'ts_error' },
        { text: 'Только с полными данными', value: 'complete_data' },
      ],

      // Заголовки таблицы
      tableHeaders: [
        {
          text: "Месторождение",
          value: "field",
          width: "15%",
          class: "pl-6",
        },
        {
          text: "Площадка",
          value: "site",
          width: "15%",
        },
        {
          text: "Объект",
          value: "object",
          width: "25%",
        },
        {
          text: "Расчеты",
          value: "calculations",
          align: "center",
          width: "10%",
        },
        {
          text: "ДМ",
          value: "dm",
          align: "center",
          width: "10%",
        },
        {
          text: "ТС",
          value: "ts",
          align: "center",
          width: "10%",
        },
      ],
    };
  },

  computed: {
    isSummaryTableDialogVisible: {
      get() {
        return this.$store.state.dialogs.isSummaryTableDialogVisible;
      },
      set(value) {
        this.$store.commit("updateIsSummaryTableDialogVisible", value);
      },
    },

    // Фильтрованные сооружения
    filteredStructures() {
      let filtered = this.structures;

      // 1. Исключаем сооружения без данных по всем параметрам
      filtered = filtered.filter(item => 
        item.calculations !== null || 
        item.dm !== null || 
        item.ts !== null
      );

      // 2. Исключаем сооружения без отклонений (только те, у которых есть хотя бы одно отклонение)
      filtered = filtered.filter(item => 
        item.calculations === false || 
        item.dm === false || 
        item.ts === false
      );

      // 3. Применяем активные фильтры
      this.activeFilters.forEach(filter => {
        switch (filter) {
          case 'calculations_error':
            filtered = filtered.filter(item => item.calculations === false);
            break;
          case 'dm_error':
            filtered = filtered.filter(item => item.dm === false);
            break;
          case 'ts_error':
            filtered = filtered.filter(item => item.ts === false);
            break;
          case 'complete_data':
            filtered = filtered.filter(item => 
              item.calculations !== null && 
              item.dm !== null && 
              item.ts !== null
            );
            break;
        }
      });

      return filtered;
    }
  },

  methods: {
    ...mapMutations(["setSnack"]),

    /**
     * Закрытие диалогового окна сводной таблицы
     */
    closeSummaryTableDialog() {
      this.isSummaryTableDialogVisible = false;
    },

    /**
     * Загрузка данных с бэкенда
     */
    async loadStructuresData() {
      // Таймаут для защиты от бесконечной загрузки
      const timeoutPromise = new Promise((_, reject) => 
        setTimeout(() => reject(new Error('Timeout')), 30000)
      );

      this.loading = true;
      try {
        const apiPromise = this.$api.get('/api/structures/deviations');
        const response = await Promise.race([apiPromise, timeoutPromise]);
        
        // Валидация и преобразование данных
        const validData = response.data.filter(item => this.validateStructureData(item));
        
        this.structures = validData.map(item => ({
          id: item.id,
          field: item.license_area || item.field,
          site: item.project || item.site,
          object: item.building || item.picket_pipeline || item.object,
          calculations: this.parseCalculationStatus(item.calculation_status),
          dm: this.parseDMStatus(item.dm_data),
          ts: this.parseTSStatus(item.ts_status),
          // Дополнительные данные для навигации
          structureId: item.structure_id,
          projectId: item.project_id
        }));

        this.totalStructures = this.structures.length;

      } catch (error) {
        console.error('Ошибка загрузки данных:', error);
        let message = 'Ошибка загрузки данных таблицы';
        
        if (error.message === 'Timeout') {
          message = 'Превышено время ожидания загрузки данных';
        } else if (error.response?.status === 404) {
          message = 'Данные не найдены';
        } else if (error.response?.status >= 500) {
          message = 'Ошибка сервера. Попробуйте позже';
        }
        
        this.setSnack({
          message,
          color: 'error'
        });
      } finally {
        this.loading = false;
      }
    },

    /**
     * Валидация данных сооружения
     */
    validateStructureData(data) {
      const requiredFields = ['id', 'field', 'site', 'object'];
      return requiredFields.every(field => data[field] != null);
    },

    /**
     * Парсинг статуса расчетов
     */
    parseCalculationStatus(status) {
      if (status === undefined || status === null) return null;
      
      // Уточнить у бэкенда возможные статусы
      const successStatuses = ['success', 'completed', 'ok', true];
      const errorStatuses = ['error', 'failed', 'deviation', false];
      
      if (successStatuses.includes(status)) return true;
      if (errorStatuses.includes(status)) return false;
      
      return null; // Неизвестный статус
    },

    /**
     * Парсинг статуса ДМ
     */
    parseDMStatus(dmData) {
      if (!dmData || dmData.length === 0) return null;
      
      // Анализируем сводную таблицу по ДМ
      // Предполагаем, что есть отклонения если хотя бы одна запись имеет отклонение
      const hasDeviations = dmData.some(record => 
        record.has_deviation === true || 
        record.status === 'error' ||
        record.deviation_count > 0 ||
        record.is_deviated === true
      );
      return !hasDeviations; // true - всё ок, false - есть отклонения
    },

    /**
     * Парсинг статуса ТС
     */
    parseTSStatus(tsData) {
      if (!tsData || tsData.length === 0) return null;
      
      // Анализируем поле "статус" в сводной таблице по ТС
      const hasDeviations = tsData.some(record => 
        record.status === 'error' || 
        record.status === 'deviation' ||
        record.has_issues === true ||
        record.is_problematic === true
      );
      return !hasDeviations; // true - всё ок, false - есть отклонения
    },

    /**
     * Переключение фильтра
     */
    toggleFilter(filterValue) {
      const index = this.activeFilters.indexOf(filterValue);
      if (index > -1) {
        this.activeFilters.splice(index, 1);
      } else {
        this.activeFilters.push(filterValue);
      }
    },

    /**
     * Навигация к карточке сооружения
     */
    navigateToStructure(structure) {
      try {
        // TODO: Реализовать навигацию к карточке сооружения
        // Это может быть переход по маршруту или открытие другого диалога
        console.log('Переход к сооружению:', structure);
        
        // Пример навигации:
        if (structure.structureId) {
          // this.$router.push(`/structure/${structure.structureId}`);
          // или
          // this.$emit('openStructureCard', structure);
        }
        
        this.setSnack({
          message: `Переход к объекту: ${structure.object}`,
          color: 'info'
        });
      } catch (error) {
        console.error('Ошибка навигации:', error);
        this.setSnack({
          message: 'Ошибка перехода к объекту',
          color: 'error'
        });
      }
    }
  },

  watch: {
    isSummaryTableDialogVisible(newVal) {
      if (newVal) {
        this.loadStructuresData();
      } else {
        // Сбрасываем фильтры при закрытии
        this.activeFilters = [];
        this.pagination = {};
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.table-gtm-elements ::v-deep tr > td:first-child {
  padding-left: 24px;
}

.filter-chips {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

.object-link {
  color: var(--v-info-base);
  text-decoration: underline;
  cursor: pointer;
  
  &:hover {
    color: var(--v-primary-darken1);
    text-decoration: none;
  }
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
  border-bottom: 2px solid #e0e0e0 !important;
  height: 44px !important;
  font-weight: 600;
  background-color: #f5f5f5;
}

.v-data-table ::v-deep td {
  border-bottom: 1px solid #e0e0e0 !important;
  height: 52px;
}

.v-data-table ::v-deep tbody tr:hover {
  background-color: #f8f9fa;
}

.download-btn {
  width: 116px;
  height: 40px;
  color: #fff;
}

// Стили для иконок статусов
.v-icon.success {
  color: #4caf50;
}

.v-icon.error {
  color: #f44336;
}

// Стили для состояния пустой таблицы
.v-data-table ::v-deep .v-data-table__empty-wrapper {
  background-color: #fafafa;
}
</style>
