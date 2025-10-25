# Maven坐标

```xml
<!-- poi -->
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
    <version>${poi}</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
    <version>${poi}</version>
</dependency>
```

```java
private final String excelPath = "src/main/resources/Excel/Excel_File/";

@Test
void writeExcel() {
    // 创建一个工作簿
    XSSFWorkbook workbook = new XSSFWorkbook();
    // 创建一个工作表
    XSSFSheet mySheet = workbook.createSheet("MySheet");
    for (int i = 0; i < 10; i++) {
        // 创建一行
        XSSFRow row = mySheet.createRow(i);
        for (int j = 0; j < 10; j++) {
            // 创建一个单元格
            row.createCell(j).setCellValue("(" + (i + 1) + "," + (j + 1) + ")");
        }
    }
    try (FileOutputStream fos = new FileOutputStream(excelPath + "Excel.xlsx")) {
        workbook.write(fos);
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        try {
            workbook.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

@Test
void readExcel() {
    try (FileInputStream fis = new FileInputStream(excelPath + "Excel.xlsx");
         // 使用WorkbookFactory创建工作簿对象，自动适配不同的Excel格式
         Workbook workbook = WorkbookFactory.create(fis)) {
        Sheet mySheet = workbook.getSheet("MySheet");
        for (int i = 0; i <= mySheet.getLastRowNum(); i++) {
            Row row = mySheet.getRow(i);
            for (int j = 0; j < row.getLastCellNum(); j++) {
                System.out.print(row.getCell(j) + " ");
            }
            System.out.println();
        }
    } catch (Exception e) {
        e.printStackTrace();
    }
}

@Test
void typeExcel() {
    try (FileInputStream fis = new FileInputStream(excelPath + "Excel.xlsx");
         Workbook workbook = WorkbookFactory.create(fis)) {
        Sheet sheet = workbook.getSheetAt(0);

        // 遍历所有行
        for (Row row : sheet) {
            for (Cell cell : row) {
                // 根据单元格类型处理数据
                switch (cell.getCellType()) {
                    case STRING:
                        System.out.print(cell.getStringCellValue() + "\t");
                        break;
                    case NUMERIC:
                        if (DateUtil.isCellDateFormatted(cell)) {
                            System.out.print(cell.getDateCellValue() + "\t");
                        } else {
                            System.out.print(cell.getNumericCellValue() + "\t");
                        }
                        break;
                    case BOOLEAN:
                        System.out.print(cell.getBooleanCellValue() + "\t");
                        break;
                    default:
                        System.out.print("未知类型\t");
                }
            }
            System.out.println();
        }
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```
