使用`PHPOffice/PhpSpreadsheet`类 [传送](https://github.com/PHPOffice/PhpSpreadsheet)

单个sheet导入 取出数据
```
use PhpOffice\PhpSpreadsheet\IOFactory;

public static function import($filename)
{
    // set_time_limit(0);
    
    $sheet = IOFactory::load($filename);
    $data = $sheet->getActiveSheet()->toArray();
    
    return $data;
}
```

单个sheet导出(保存为文件在服务端)
```
use PhpOffice\PhpSpreadsheet\IOFactory;
use PhpOffice\PhpSpreadsheet\Spreadsheet;
use PhpOffice\PhpSpreadsheet\Worksheet\Worksheet;

const DEFAULT_CELL_WIDTH = 20;

public static function export($data, $filename)
{
    // set_time_limit(0);

    $sheet = new Spreadsheet();
    self::setData($sheet, $data);
    self::setStyle($sheet, $data[0]);
    
    $saveName = '...';
    $writer = IOFactory::createWriter($sheet, 'Xlsx');
    $writer->save($saveName);
}

/**
 * 填充数据 应保证key为0指向的是表头数组
 * @param Spreadsheet $sheet
 * @param array $data 二维关联数组 第一层为数字索引 第二层为字段名键值
 */
private static function setData($sheet, $data)
{
    /** @var Worksheet $activeSheet */
    $activeSheet = $sheet->setActiveSheetIndex(0);
    $rowCount = count(current($data));
    
    foreach ($data as $key => $element)
    {
        $column = 'A';
        $row = $key + 1;
    
        for ($i = 0; $i < $rowCount; $i++)
        {
            $activeSheet->setCellValue("{$column}{$row}", current($element));
            next($element);
            $column++;
        }
    }
}

/**
 * 设置单元格样式 
 * @param Spreadsheet $sheet
 * @param array $headers 应保证$data中key为0指向的是表头数组
 */
private static function setStyle($sheet, $headers)
{
    /** @var Worksheet $activeSheet */
    $activeSheet = $sheet->setActiveSheetIndex(0);
    $rowCount = count($headers);

    // 设置全局水平、垂直居中
    $sheet->getDefaultStyle()->getAlignment()->setHorizontal('center')->setVertical('center');
    
    // 设置单元格宽度
    $column = 'A';
    for ($i = 0; $i < $rowCount; $i++)
    {
        $activeSheet->getColumnDimension($column)->setWidth(self::DEFAULT_CELL_WIDTH);
        $column++;
    }
}
```
