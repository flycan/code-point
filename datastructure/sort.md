
```php
<?php



class RedixSort
{
    public function sort($numbers, $d) //d表示最大的数有多少位
    {
        $buckets = [];
        $bucketNum = 10;

        $m = 1;

        while($m <= $d)
        {
            for($i = 0; $i < count($numbers); $i++) 
            {
                // 1.计算余数 表示放到哪个桶中
                $bucketSort =  ($numbers[$i] / pow(10, $m - 1)) % 10;
                $buckets[$bucketSort][] = $numbers[$i];
            }

            // 2.遍历桶 再次放回number中
            
            // 这里用到了php的数组排序函数 可以使用下面这种
            // $newNumber = [];
            // ksort($buckets);
            // foreach($buckets as $bucket)
            // {
            //     $newNumber = array_merge($newNumber, $bucket);
            // }
            // $numbers = $newNumber;

            // 这里可以直接清空源数据，也可以重新赋值
            // $numbers = [];
            // for($i = 0; $i < $bucketNum; $i++)
            // {
            //     foreach($buckets[$i] as $item)
            //     {
            //         $numbers[] = $item;
            //     }
            // }

            $pos = 0;
            for($i = 0; $i < $bucketNum; $i++)
            {
                foreach($buckets[$i] as $item)
                {
                    $numbers[$pos++] = $item;
                    $k++;
                }
            }

            // 3.清空桶
            $buckets = [];
            
            $m++;
        }

        return $numbers;
    }
}

$numbers = range(5,15);

shuffle($numbers);

// print_r((new RedixSort())->sort($numbers, 2));

// 冒泡排序
function bubbleSort($arr)
{
    $len = count($arr);
    for ($i = 0; $i < $len - 1; $i++) {
        for ($j = 0; $j < $len - 1 - $i; $j++) {
            if ($arr[$j] > $arr[$j+1]) {
                $tmp = $arr[$j];
                $arr[$j] = $arr[$j+1];
                $arr[$j+1] = $tmp;
            }
        }
    }
    return $arr;
}

// print_r(bubbleSort($numbers));

// 选择排序
function selectSort($arr)
{
    $len = count($arr);
    for($i = 0; $i < $len-1; $i++)
    {
        $selectIndex = $i;

        for($j = $i+1; $j < $len; $j++)
        {
            if($arr[$selectIndex] > $arr[$j])
            {
                $selectIndex = $j;
            }
        }

        $tmp = $arr[$selectIndex];
        $arr[$selectIndex] = $arr[$i];
        $arr[$i] = $tmp;
    }
    return $arr;
}

// print_r(selectSort($numbers));

function insertionSort($arr)
{
    $len = count($arr);

    for($i = 1; $i < $len; $i++)
    {
        $insertIndex = $i;

        $current = $arr[$i];

        // 找到插入的位置
        while($insertIndex > 0 && $arr[$insertIndex-1] > $current)
        {
            $insertIndex--;
        }

        // 前面的元素网后移动
        if($insertIndex < $i) {
            $currentIndex = $i;
            while($currentIndex > $insertIndex){
                $temp = $arr[$currentIndex];
                $arr[$currentIndex] = $arr[$currentIndex-1];
                $arr[$currentIndex-1] = $temp;
                $currentIndex--;
            }
        }
    }

    return $arr;
}

// print_r(insertionSort($numbers));

// 快速排序
function quickSort($arr)
{
    if(count($arr) <=1) {
        return $arr;
    }

    $len = count($arr);

    $middle = $arr[0];

    $leftArr = [];

    $rightArr = [];

    for($i = 1; $i < $len; $i++)
    {
        if($arr[$i] > $middle) {
            $leftArr[] = $arr[$i];
        } else {
            $rightArr[] = $arr[$i];
        }
    }

    $leftArr = quickSort($leftArr);
    $leftArr[] = $middle;

    $rightArr = quickSort($rightArr);

    return array_merge($leftArr, $rightArr);
}

// 归并排序
function mergeSort($arr)
{
    $len = count($arr);
    if ($len < 2) {
        return $arr;
    }
    $middle = floor($len / 2);
    $left = array_slice($arr, 0, $middle);
    $right = array_slice($arr, $middle);
    return merge(mergeSort($left), mergeSort($right));
}

function merge($left, $right)
{
    $result = [];

    while (count($left) > 0 && count($right) > 0) {
        if ($left[0] <= $right[0]) {
            $result[] = array_shift($left);
        } else {
            $result[] = array_shift($right);
        }
    }

    while (count($left))
        $result[] = array_shift($left);

    while (count($right))
        $result[] = array_shift($right);

    return $result;
}


// print_r(quickSort($numbers));

function shellSort($arr)
{
    $len = count($arr);
    $temp = 0;
    $gap = 1;
    while($gap < $len / 3) {
        $gap = $gap * 3 + 1;
    }
    for ($gap; $gap > 0; $gap = floor($gap / 3)) {
        for ($i = $gap; $i < $len; $i++) {
            $temp = $arr[$i];
            for ($j = $i - $gap; $j >= 0 && $arr[$j] > $temp; $j -= $gap) {
                $arr[$j+$gap] = $arr[$j];
            }
            $arr[$j+$gap] = $temp;
        }
    }
    return $arr;
}

// print_r(shellSort($numbers));


function buildMaxHeap(&$arr)
{
    global $len;
    for ($i = floor($len/2); $i >= 0; $i--) {
        heapify($arr, $i);
    }
}

function heapify(&$arr, $i)
{
    global $len;
    $left = 2 * $i + 1;
    $right = 2 * $i + 2;
    $largest = $i;

    if ($left < $len && $arr[$left] > $arr[$largest]) {
        $largest = $left;
    }

    if ($right < $len && $arr[$right] > $arr[$largest]) {
        $largest = $right;
    }

    if ($largest != $i) {
        swap($arr, $i, $largest);
        heapify($arr, $largest);
    }
    print_r($arr);
}

function swap(&$arr, $i, $j)
{
    $temp = $arr[$i];
    $arr[$i] = $arr[$j];
    $arr[$j] = $temp;
}

function heapSort($arr) {
    global $len;
    $len = count($arr);
    buildMaxHeap($arr);

    // print_r($arr);

    // for ($i = count($arr) - 1; $i > 0; $i--) {
    //     swap($arr, 0, $i);
    //     $len--;
    //     heapify($arr, 0);
    // }
    return $arr;
}



// (heapSort($numbers));


// 计数排序
function countingSort($arr, $maxValue = null)
{
    if ($maxValue === null) {
        $maxValue = max($arr);
    }
    for ($m = 0; $m < $maxValue + 1; $m++) {
        $bucket[] = null;
    }


    

    $arrLen = count($arr);
    for ($i = 0; $i < $arrLen; $i++) {
        if (!array_key_exists($arr[$i], $bucket)) {
            $bucket[$arr[$i]] = 0;
        }
        $bucket[$arr[$i]]++;
    }

    print_r($bucket);

    $sortedIndex = 0;
    foreach ($bucket as $key => $len) {
        if ($len !== null) $arr[$sortedIndex++] = $key;
    }

    return $arr;
}


print_r(countingSort($numbers));
```