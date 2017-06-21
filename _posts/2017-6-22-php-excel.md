---
layout: post
title:  "PHP用PHPExcel导出excel"
categories: PHP
tags:  PHP EXCEL
---

* content
{:toc}

## 问题描述
在开发中，我们经常遇到要导出excel这个需求。这里我选用的插件地址为[excel](https://github.com/PHPOffice/PHPExcel)

<!--excerpt-->


## 使用方法
把插件下载下来，解压到工程目录。只需要Classes类。

 1. 引入类

```php
    require_once dirname(dirname(dirname(__FILE__))) . '/common/components/PHPExcel-1.8/Classes/PHPExcel.php';
    require_once dirname(dirname(dirname(__FILE__))) . '/common/components/PHPExcel-1.8/Classes/PHPExcel/IoFactory.php';
```

 2. 创建excel对象

   外部引入的类，新建对象时，在类的对象前加\。这是PHP5.3的新特性之一：斜杠 \ 表示全局命名空间,像根目录一样,叫命名空间分隔符.

```php
    $objPHPExcel = new \PHPExcel();
```

 3. 创建excel表格

```php
     $obpe_pro = $obpe->getProperties();
     $obpe_pro->setCreator('midoks')//设置创建者
         ->setLastModifiedBy('2013/2/16 15:00')//设置时间
         ->setTitle('data')//设置标题
         ->setSubject('beizhu')//设置备注
         ->setDescription('miaoshu')//设置描述
         ->setKeywords('keyword')//设置关键字 | 标记
         ->setCategory('catagory');//设置类别
    $obpe->setactivesheetindex(0);

    foreach($mulit_arr as $k=>$v){
         $k = $k+1;
         /* @func 设置列 */
         $obpe->getactivesheet()->setcellvalue('A'.$k, $v[0]);
         $obpe->getactivesheet()->setcellvalue('B'.$k, $v[1]);
         $obpe->getactivesheet()->setcellvalue('C'.$k, $v[2]);
    ｝
}
```

 4. 创建多个表格

 ```php
     //创建一个新的工作空间(sheet)
     $obpe->createSheet();
     $obpe->setactivesheetindex(1);
 ```

## 完整代码

```php
        /** Error reporting */
        error_reporting(E_ALL);
        ini_set('display_errors', TRUE);
        ini_set('display_startup_errors', TRUE);
        date_default_timezone_set('Europe/London');

        if (PHP_SAPI == 'cli')
            die('This example should only be run from a Web Browser');

        /** Include PHPExcel */
        require_once dirname(dirname(dirname(__FILE__))) . '/common/components/PHPExcel-1.8/Classes/PHPExcel.php';
        require_once dirname(dirname(dirname(__FILE__))) . '/common/components/PHPExcel-1.8/Classes/PHPExcel/IoFactory.php';
        //echo dirname(dirname(dirname(__FILE__))) . '/common/components/PHPExcel-1.8/Classes/PHPExcel.php';

        $objPHPExcel = new \PHPExcel();

     // Set document properties
        $objPHPExcel->getProperties()->setCreator("Maarten Balliauw")
            ->setLastModifiedBy("Maarten Balliauw")
            ->setTitle("Office 2007 XLSX Test Document")
            ->setSubject("Office 2007 XLSX Test Document")
            ->setDescription("Test document for Office 2007 XLSX, generated using PHP classes.")
            ->setKeywords("office 2007 openxml php")
            ->setCategory("Test result file");


        //部门分配情况
        $objPHPExcel->setActiveSheetIndex(0)
            ->setCellValue('A1', '1')
            ->setCellValue('B1', '2')
            ->setCellValue('C1', '3')
            ->setCellValue('D1', '4')
            ->setCellValue('E1', '5')
            ->setCellValue('F1', '6')
            ->setCellValue('G1', '7')
            ->setCellValue('H1', '8')
            ->setCellValue('I1', '9')
            ->setCellValue('J1', '10');

        $objPHPExcel->setActiveSheetIndex(0);
        $i = 2;
        foreach ($data as $item) {
            $objPHPExcel->setActiveSheetIndex(0)
                ->setCellValue('A'.$i, $item['name'])
                ->setCellValue('B'.$i, $item['total'])
                ->setCellValue('C'.$i, $item['qqSource'])
                ->setCellValue('D'.$i, $item['400Source'])
                ->setCellValue('E'.$i, $item['mobilSource'])
                ->setCellValue('F'.$i, $item['pushSource'])
                ->setCellValue('G'.$i, $item['stockSource'])
                ->setCellValue('H'.$i, $item['registerSource'])
                ->setCellValue('I'.$i, $item['handSource'])
                ->setCellValue('J'.$i, $item['otherSource']);
            $i++;
        }
        $objPHPExcel->getSheet(0)->setTitle('投顾分配情况');


        header('Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet');
        header('Content-Disposition: attachment;filename="投顾分配情况.xlsx"');
        header('Cache-Control: max-age=0');
        header('Cache-Control: max-age=1');
        header ('Expires: Mon, 26 Jul 1997 05:00:00 GMT'); // Date in the past
        header ('Last-Modified: '.gmdate('D, d M Y H:i:s').' GMT'); // always modified
        header ('Cache-Control: cache, must-revalidate'); // HTTP/1.1
        header ('Pragma: public'); // HTTP/1.0

        $objWriter = \PHPExcel_IOFactory::createWriter($objPHPExcel, 'Excel2007');
        $objWriter->save('php://output');
        exit;

```



## 遇到的问题
PHPExcel不能用ajax来提交！！！，如果用ajax不能下载那是正常的！我是把ajax提交改成表单提交，然后就成功了！
