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
У меня есть такая задача:
Мой backend на Java 8 работает с Excel файлом, который содержит в себе выпадающий список различных значений
Этот же список можно просматривать не только в Excel, но и на frontend части приложения
А поскольку мне приходилось менять этот список, то нужно было сдлеать так, чтобы старые значения в нём больше не содержались
(но если они были выбраны ранее, то их нужно отображать, но когда пользователь откроет выпадающую часть, там должны быть только новые значения)
И вот для решения этой проблемы я создал в базе данных boolean-столбец is_deprecated, для которого true-значение обозначает старое
В файле миграции я отметил старые значения как true, и на frontend-части стало отображаться всё отлично.
Но возникла проблема с отображением в Excel, поскольку сейчас там и новые значения, и старые.
Давай сначала приведу код, с которым нужно будет порабоать:
1) В классе GroundThermalStabilizerService нас интересует метод getValuesForSelected:
/**
 * Сервис - Термостабилизатор грунта (ТСГ)
 */
@Service
@Validated(CardValidationSequence.class)
public class GroundThermalStabilizerService {
    @Autowired
    private ObjectCreatorChain objectCreatorChain;
    @Autowired
    private GroundThermalStabilizerRepository groundThermalStabilizerRepository;
    @Autowired
    private GroundThermalStabilizerElementRepository gtseRepository;
    @Autowired
    private TreeNodeRepository treeNodeRepository;
    @Autowired
    private DictGroundThermalStabilizerTypeRepository dictGroundThermalStabilizerTypeRepository;

    @Autowired
    private DictPerformanceIndicatorRepository dictPerformanceIndicatorRepository;
    @Autowired
    private DictHeatInsulatingSleeveRepository dictHeatInsulatingSleeveRepository;
    @Autowired
    private DictClimaticVersionTypeRepository dictClimaticVersionTypeRepository;
    @Autowired
    private ReliabilityIndicatorsService reliabilityIndicatorsService;

    @Autowired
    private GtsVisualInspectionService gtsVisualInspectionService;


    /**
     * Получение свойств термостабилизатора грунта (ТСГ), включая данные из TreeNode
     *
     * @param strObjectId идентификатор термостабилизатора грунта
     * @return Объект {@link DeformationMarkCardDto} который оостветствует переданному идентификатору
     * @throws BusinessException
     */
    public GroundThermalStabilizerCardDto getPropertiesObject(String strObjectId) throws BusinessException, IllegalArgumentException {
        UUID objectId = UUID.fromString(strObjectId);
        GroundThermalStabilizer tsg = groundThermalStabilizerRepository.findById(objectId)
                .orElseThrow(() -> new NoSuchElementException("Не удалось найти объект"));
        TreeNode treeNode = treeNodeRepository.getByObjectId(objectId);
        if (treeNode == null) {
            throw new BusinessException("Не удалось найти объект в дереве объектов");
        }
        GtsVisualInspectionDto gtsVisualInspectionDto = gtsVisualInspectionService.getLastVisualInspection(UUID.fromString(strObjectId));
        return new GroundThermalStabilizerCardDto(new TreeNodeDto(treeNode), new GroundThermalStabilizerPropertiesDto(tsg, gtsVisualInspectionDto));
    }

    /**
     * Получить все значения выпадающих списков сгруппированых
     * по свойствам для термостабилизатора грунта (ТСГ)
     *
     * @return {@link BuildingListValuesForSelectDto}
     */
    public GroundThermalStabilizerListsValuesForSelectDto getValuesForSelected() {
        return new GroundThermalStabilizerListsValuesForSelectDto(
                dictGroundThermalStabilizerTypeRepository.findAll(),
                dictPerformanceIndicatorRepository.findAllByIsDeprecatedFalse(),
                dictHeatInsulatingSleeveRepository.findAll(),
                dictClimaticVersionTypeRepository.findAll()
        );
    }

    /**
     * Редактирование термостабилизатора грунта (ТСГ)
     *
     * @param tsgCardDto {@link GroundThermalStabilizerCardDto} объект с обновлеными полями
     * @return сохраненый объект {@link GroundThermalStabilizerCardDto}
     * @throws NotSupportedException
     */
    public Object updateGroundThermalStabilizer(@Valid GroundThermalStabilizerCardDto tsgCardDto) throws NotSupportedException {
        return objectCreatorChain.updateObject(tsgCardDto, Category.Constants.TSG_ID);
    }

    /**
     * Получить элементы сети гтм объекта определенного вида и категорией, с сортировкой и поиском
     */
    public Page<GroundThermalStabilizerElementDto> getElementsNetworkByTreeNodeIdAndCategoryIdWithMultiFieldSearch(UUID treeNodeId, UUID categoryId, int page, int size, String sortBy, boolean sortDesc, String searchText) {
        Sort.Direction direction = sortDesc ? Sort.Direction.DESC : Sort.Direction.ASC;
        TypedParameterValue tpvParentNodeId = new TypedParameterValue(PostgresUUIDType.INSTANCE, treeNodeId);
        TypedParameterValue tpvCategoryId = new TypedParameterValue(PostgresUUIDType.INSTANCE, categoryId);
        String preparedSearchText = searchText.toLowerCase();
        Page<GroundThermalStabilizerElement> pageGroundThermalStabilizerElement = gtseRepository.findAllByParentIdAndCategoryIdByMultipleFieldSearchText(
                GroundThermalStabilizerElement.class,
                tpvParentNodeId,
                tpvCategoryId,
                preparedSearchText,
                PageRequest.of(page, size),
                direction,
                sortBy);

        return new PageImpl<>(pageGroundThermalStabilizerElement.stream()
                .map(this::mapToGroundThermalStabilizerElementDto)
                .collect(Collectors.toList()),
                PageRequest.of(page, size), pageGroundThermalStabilizerElement.getTotalElements());
    }

    /**
     * Получить элементы сети гтм объекта определенного вида и категорией, с сортировкой
     */
    public Page<GroundThermalStabilizerElementDto> getElementsNetworkByTreeNodeIdAndCategoryId(UUID treeNodeId, UUID categoryId, int page, int size, String sortBy, boolean sortDesc) {
        Sort.Direction direction = sortDesc ? Sort.Direction.DESC : Sort.Direction.ASC;
        TypedParameterValue tpvParentNodeId = new TypedParameterValue(PostgresUUIDType.INSTANCE, treeNodeId);
        TypedParameterValue tpvCategoryId = new TypedParameterValue(PostgresUUIDType.INSTANCE, categoryId);
        Page<GroundThermalStabilizerElement> pageGroundThermalStabilizerElement = gtseRepository.findAllByParentIdAndCategoryId(
                GroundThermalStabilizerElement.class,
                tpvParentNodeId,
                tpvCategoryId,
                PageRequest.of(page, size),
                direction,
                sortBy);

        return new PageImpl<>(pageGroundThermalStabilizerElement.stream()
                .map(this::mapToGroundThermalStabilizerElementDto)
                .collect(Collectors.toList()),
                PageRequest.of(page, size), pageGroundThermalStabilizerElement.getTotalElements());
    }

    private GroundThermalStabilizerElementDto mapToGroundThermalStabilizerElementDto(GroundThermalStabilizerElement gtsElement) {
        // TODO: 07.07.2023 сделать чтобы в BaseElement был приджойнин узел текущего элемента
        TreeNode treeNode = treeNodeRepository.findById(gtsElement.getId())
                .orElseThrow(() -> new BusinessException("Не удалось найти узел дерева объектов"));
        return gtsElement.toGroundThermalStabilizerElementDto(reliabilityIndicatorsService.calculateTextColor(treeNode));
    }
}
2) В классе BlankExporter нас интересует метод fillDataSheets (а именно строка String[] names = chainRepository.findAll().stream().map(BaseDictionary::getName).toArray(String[]::new)):
public class BlankExporter extends XlsxProcessor {
    private final List<OperationBlankField> fields;
    private final Category category;
    private final TreeNode projectTreeNode;
    private final Repositories repositories;
    private final OperationBlank operationBlank;
    public static final Map<String, List<String>> picketsMap = new HashMap<>();
    public static final Map<String, List<String>> buildingsMap = new HashMap<>();

    public BlankExporter(OperationBlank operationBlank, TreeNode projectTreeNode, Repositories repositories) throws IOException {
        this.operationBlank = operationBlank;
        this.fields = operationBlank.getFields().stream().sorted(Comparator.comparing(OperationBlankField::getOrder)).collect(Collectors.toList());
        this.category = operationBlank.getCategory();
        this.projectTreeNode = projectTreeNode;
        this.repositories = repositories;
        super.createNewBook();
    }

    /**
     * Подготовка скрытых листов бланка с системными данными: элементы сети ГТМ, значения перечисляемых свойств
     */
    public void fillDataSheets() {
        TreeNodeRepository treeNodeRepository = (TreeNodeRepository) repositories.getRepositoryFor(TreeNode.class).get();
        //todo переделать во что-то более красивое - может добавить children category у бланка и если задана, то заполнять особым образом
        if (!operationBlank.getId().equals(BlankIdConstant.BUIDLINGS_PILE_GROUP_BLANK_ID)) {
            List<String> objectsNamesUnderProject = treeNodeRepository.findObjectsUnderProject(projectTreeNode.getObjectId(), category.getId());
            fillHiddenSheet(category.getName(), objectsNamesUnderProject.toArray(new String[0]));
        } else {
            getCodes();
            List<TreeNode> buildingsTreeNodesUnderProject = treeNodeRepository.findObjectsUnderProjectByCategories(projectTreeNode.getObjectId(), new ArrayList<UUID>() {{
                add(category.getId());
            }});
            PileGroupRepository pileGroupRepository = (PileGroupRepository) repositories.getRepositoryFor(PileGroup.class).get();
            List<UUID> buildingsTreeNodesUnderProjectIds = buildingsTreeNodesUnderProject.stream().map(x -> x.getObjectId()).collect(Collectors.toList());
            List<PileGroup> pileGroups = pileGroupRepository.findAllByBuildingIdIn(buildingsTreeNodesUnderProjectIds);
            List<String> objectsNamesUnderProject = new ArrayList<>();
            buildingsTreeNodesUnderProject.forEach(x -> {
                String buildingCode = x.getCode();
                pileGroups.stream().filter(y -> y.getBuilding().getId().equals(x.getObjectId())).forEach(y -> {
                    objectsNamesUnderProject.add(String.format("%s, " + " %s", buildingCode, y.getPileGroupName()));
                });
            });

            fillHiddenSheet(category.getName(), objectsNamesUnderProject.toArray(new String[0]));
        }
        fields.forEach(field -> {
            if (field.getIsDictionary()) {
                try {
                    Class<?> dictClass = Class.forName("ru.tomsknipi.gtm.model.dbo.dictionary." + field.getDictionaryName());
                    JpaRepository<BaseDictionary, UUID> chainRepository = (JpaRepository<BaseDictionary, UUID>) repositories.getRepositoryFor(dictClass).get();
                    String[] names = chainRepository.findAll().stream().map(BaseDictionary::getName).toArray(String[]::new);
                    fillHiddenSheet(field.getDisplayName(), names);
                } catch (ClassNotFoundException e) {
                    e.printStackTrace();
                }
            }
        });
    }

    private void fillHidden(List<String> objectsNamesUnderProject) {
        fillHiddenSheet(category.getName(), objectsNamesUnderProject.toArray(new String[0]));
        fields.forEach(field -> {
            if (field.getIsDictionary()) {
                try {
                    Class<?> dictClass = Class.forName("ru.tomsknipi.gtm.model.dbo.dictionary." + field.getDictionaryName());
                    JpaRepository<BaseDictionary, UUID> chainRepository = (JpaRepository<BaseDictionary, UUID>) repositories.getRepositoryFor(dictClass).get();
                    String[] names = chainRepository.findAll().stream().map(BaseDictionary::getName).toArray(String[]::new);
                    fillHiddenSheet(field.getDisplayName(), names);
                } catch (ClassNotFoundException e) {
                    e.printStackTrace();
                }
            }
        });
    }

    /**
     * Подготовка скрытых листов бланка у Групп свай с системными данными: элементы сети ГТМ, значения перечисляемых свойств
     */
    public void fillDataSheetForPileGroup() {
        List<String> result = getCodes();
        fillHidden(result);
    }

    public List<String> getCodes() {
        TreeNodeRepository treeNodeRepository = (TreeNodeRepository) repositories.getRepositoryFor(TreeNode.class).get();
        PileGroupRepository pileGroupRepository = (PileGroupRepository) repositories.getRepositoryFor(PileGroup.class).get();
        List<String> objectsNamesUnderProject = treeNodeRepository.findObjectsUnderProject(projectTreeNode.getObjectId(), category.getId());
        List<String> result = new ArrayList<>();

        if (category.getId().equals(Category.Constants.PICKETS_ID)) {
            BlankImporter.putObjectForMap(treeNodeRepository, pileGroupRepository, objectsNamesUnderProject, result, picketsMap, category);
        } else if (category.getId().equals(Category.Constants.BUILDING_ID)) {
            BlankImporter.putObjectForMap(treeNodeRepository, pileGroupRepository, objectsNamesUnderProject, result, buildingsMap, category);
        }
        return result;
    }

    /**
     * Подготовка листов бланка для каждой категории в переданном шаблоне операции
     */
    private void fillCategorySheets() {
        createAndSetActiveSheet(category.getName(), k.row.COUNT_FOR_PRINT, fields.size() + k.col.COUNT_COMMON);
        if (!this.operationBlank.getId().equals(BlankIdConstant.BUIDLINGS_PILE_GROUP_BLANK_ID)) {
            prepareHeader();
            prepareTable();
            prepareEnumerationConstraints();
        } else {
            prepareHeaderPile();
            prepareTableForPileGroup();
            prepareEnumerationConstraintsPile();
        }
        autoSizeColumns(fields.size() + k.col.COUNT_COMMON);
        resizeHeaders(this.operationBlank.getOperationBlankType(), this.operationBlank.getCategory().getId());
    }

    private void fillCategorySheetsForPileGroup() {
        createAndSetActiveSheet(category.getName(), k.row.COUNT_FOR_PRINT, fields.size() + k.colForPile.COUNT_COMMON);
        prepareHeaderPile();
        prepareTableForPileGroup();
        prepareEnumerationConstraintsPile();
        autoSizeColumns(fields.size() + k.colForPile.COUNT_COMMON);
        resizeHeadersPile(this.operationBlank.getOperationBlankType(), this.operationBlank.getCategory().getId());
    }


    /**
     * Заполнение листа с описанием бланка
     */
    public void fillTemplateSheet() {
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("dd.MM.yyyy");

        int colCount = 3; // - кол. колонок на листе информации включая левую пустую
        createAndSetActiveSheet(k.sheet.INFO, k.row.COUNT_FOR_PRINT, colCount);

        getHeaderCell(k.row.info.TEMPLATE_CODE, k.col.info.HEADER, false).setCellValue(k.val.template.CODE);
        getValueCell(k.row.info.TEMPLATE_CODE, k.col.info.VALUE).setCellValue(this.operationBlank.getId().toString());

        getHeaderCell(k.row.info.TEMPLATE_NAME, k.col.info.HEADER, false).setCellValue(k.val.template.NAME);
        getValueCell(k.row.info.TEMPLATE_NAME, k.col.info.VALUE).setCellValue(this.operationBlank.getName());

        getHeaderCell(k.row.info.DATE_FORMAT, k.col.info.HEADER, false).setCellValue(k.val.template.DATE_FORMAT);
        getValueCell(k.row.info.DATE_FORMAT, k.col.info.VALUE).setCellValue("dd.MM.yyyy");

        getHeaderCell(k.row.info.DATE_EXAMPLE, k.col.info.HEADER, false).setCellValue(k.val.template.DATE_EXAMPLE);
        getValueCell(k.row.info.DATE_EXAMPLE, k.col.info.VALUE).setCellValue(simpleDateFormat.format(new Date()));

        getHeaderCell(k.row.info.PROJECT_CODE, k.col.info.HEADER, false).setCellValue(k.val.project.CODE);
        getValueCell(k.row.info.PROJECT_CODE, k.col.info.VALUE).setCellValue(projectTreeNode.getCode());

        getHeaderCell(k.row.info.PROJECT_NAME, k.col.info.HEADER, false).setCellValue(k.val.project.NAME);
        getValueCell(k.row.info.PROJECT_NAME, k.col.info.VALUE).setCellValue(projectTreeNode.getName());

        activeSheet().protectSheet("password");
        autoSizeColumns(colCount);
        autoSizeRows(k.row.COUNT_FOR_PRINT);
    }


    private void prepareHeader() {
        int defaultPropsCount = k.col.DATE;

        getHeaderCell(k.row.HEADER, k.col.PICKED_OBJECT, true).setCellValue(k.val.PICKED_OBJECT);
        getHeaderCell(k.row.HEADER, k.col.INPUTTED_OBJECT, true).setCellValue(k.val.INPUTTED_OBJECT);

        if (this.operationBlank.getOperationBlankType() != OperationBlankType.CARD || this.operationBlank.getCategory().getId().equals(Category.Constants.SG_ID)) {
            getHeaderCell(k.row.HEADER, defaultPropsCount, true).setCellValue(k.val.DATE);
            defaultPropsCount++;
        }

        for (OperationBlankField field : fields) {
            getHeaderCell(k.row.HEADER, defaultPropsCount++, true).setCellValue(field.getDisplayName());
        }
    }

    private void prepareHeaderPile() {
        int defaultPropsCount = k.colForPile.DATE;
        getHeaderCell(k.row.HEADER, k.colForPile.PICKED_OBJECT, true).setCellValue(k.val.PICKED_PILE_GROUP);

        for (OperationBlankField field : fields) {
            getHeaderCell(k.row.HEADER, defaultPropsCount++, true).setCellValue(field.getDisplayName());
        }
    }

    /**
     * Подготовка таблицы
     */
    private void prepareTable() {
        for (int nn = 1; nn <= k.row.COUNT; nn++) {
            int rowIndex = k.row.VALUE + nn - 1;
            int defaultPropsCount = k.col.DATE;
            getValueCell(rowIndex, k.col.PICKED_OBJECT);
            getValueCell(rowIndex, k.col.INPUTTED_OBJECT);

            if (this.operationBlank.getOperationBlankType() != OperationBlankType.CARD || this.operationBlank.getCategory().getId().equals(Category.Constants.SG_ID)) {
                getValueCell(rowIndex, defaultPropsCount++);
            }

            for (OperationBlankField field : fields) {
                int colIndex = defaultPropsCount++;
                getValueCell(rowIndex, colIndex);
            }
        }
    }

    private void prepareTableForPileGroup() {
        for (int nn = 1; nn <= k.row.COUNT; nn++) {
            int rowIndex = k.row.VALUE + nn - 1;
            int defaultPropsCount = k.colForPile.DATE;
            getValueCell(rowIndex, k.colForPile.PICKED_OBJECT);

            for (OperationBlankField field : fields) {
                int colIndex = defaultPropsCount++;
                getValueCell(rowIndex, colIndex);
            }
        }
    }

    /**
     * Применяет для ограничения ввода значений системные данные
     */
    private void prepareEnumerationConstraints() {
        getSheet(category.getName());
        applyEnumerationConstraint(category.getName(), k.col.PICKED_OBJECT);
        int defaultPropsCount = this.operationBlank.getOperationBlankType() != OperationBlankType.CARD || this.operationBlank.getCategory().getId().equals(Category.Constants.SG_ID) ? k.col.DATE + 1 : k.col.DATE;

        for (OperationBlankField field : fields) {
            if (field.getIsDictionary()) {
                applyEnumerationConstraint(field.getDisplayName(), defaultPropsCount);
            }
            defaultPropsCount++;
        }
    }

    private void prepareEnumerationConstraintsPile() {
        getSheet(category.getName());
        applyEnumerationConstraint(category.getName(), k.colForPile.PICKED_OBJECT);
        int defaultPropsCount = this.operationBlank.getOperationBlankType() != OperationBlankType.CARD || this.operationBlank.getCategory().getId().equals(Category.Constants.SG_ID) ? k.colForPile.DATE + 1 : k.colForPile.DATE;

        for (OperationBlankField field : fields) {
            if (field.getIsDictionary()) {
                applyEnumerationConstraint(field.getDisplayName(), defaultPropsCount);
            }
            defaultPropsCount++;
        }
    }


    public void export(HttpServletResponse response) throws IOException {
        removeDefaultSheet();
        fillDataSheets();
        fillTemplateSheet();
        fillCategorySheets();

        ServletOutputStream outputStream = response.getOutputStream();

        super.writeAndCloseBook(outputStream);
        outputStream.close();
    }

    public void exportForPileGroup(HttpServletResponse response) throws IOException {
        removeDefaultSheet();
        fillDataSheetForPileGroup();
        fillTemplateSheet();
        fillCategorySheetsForPileGroup();

        ServletOutputStream outputStream = response.getOutputStream();

        super.writeAndCloseBook(outputStream);
        outputStream.close();
    }
}
3) Интерфейс DictPerformanceIndicatorRepository:
public interface DictPerformanceIndicatorRepository extends JpaRepository<DictPerformanceIndicator, UUID>,
        DictionaryRepository<DictPerformanceIndicator> {
        List<DictPerformanceIndicator> findAllByIsDeprecatedFalse();
}
Поскольку chainRepository вызывает метод findAll, то, разумеется, из интерфеса DictPerformanceIndicatorRepository будет вызываться метод findAll.
И именно в этом проблема, что метод findAll возвращает как старые значения, так и новые.
Какие у меня есть идеи по исправлению этого недочёта:
1) Наверное, самое наилучшее решение - это придумать что-то с использованием метода filter (скорее всего как-то использовать is_deprecated = false, тобы показывать только новые значения) строке String[] names = chainRepository.findAll().stream().map(BaseDictionary::getName).toArray(String[]::new),
чтобы не нарушить логику вызова метода findAll, как минимум, в методе getValuesForSelected класса GroundThermalStabilizerService
2) Возможно вообще убрать из интерфейса метод findAllByIsDeprecatedFalse и вызывать для него фильтрацию где используется
Ну а может ты предложишь какое-то лаконичное решение, тут главное не навредить уже существующему код
P.s. Постарался дать наиболее подробное описание проблемы
