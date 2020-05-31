# 冒泡排序

```<?php
<?php
/**
 * 时间复杂度 O(n²)，变种最好能到O(n)
 * 空间复杂度 O(1)
 */
class BubbleSort {
    public static function sort($data) {
        $length = count($data);
        if ($length <= 1) {
            return $data;
        }
        for ($i=0; $i < $length - 1; $i++) {
            $noSwap = true;
            for ($k=0; $k < $length - $i - 1; $k++) { 
                if ($data[$k + 1] < $data[$k]) {
                    $tmp = $data[$k];
                    $data[$k] = $data[$k + 1];
                    $data[$k + 1] = $tmp;
                    $noSwap = false;
                }
            }
            if ($noSwap) {
                break;
            }
        }
        return $data;
    }
}
```

# 插入排序

```
<?php
/**
 * 时间复杂度 O(n)（已经有序）到O(n²)
 * 空间复杂度 O(1)
 */
class InsertSort 
{
    public static function sort($data) {
        $length = count($data);
        if ($length <= 1) {
            return $data;
        }
        for ($i=1; $i < $length; $i++) { 
            $tmp = $data[$i];
            for ($k=$i - 1; $k >= 0; $k--) { 
                if ($tmp < $data[$k]) {
                    $data[$k+1] = $data[$k];
                    $data[$k] = $tmp;
                } else {
                    break;
                }
            }
        }
        return $data;
    }
}
```

# 快速排序

```
<?php
/**
 * 时间复杂度：O(nlog2n)到O(n²)：每次都选中极值
 * 空间复杂度：O(log2n))到n
 */
class QuickSort
{
    public static function sort($arr) {
        $length = count($arr);
        if ($length <= 1) {
            return $arr;
        }
        $left  = [];
        $right = [];
        $index = $arr[0];
        for ($k=1; $k < $length; $k++) { 
            if ($arr[$k] <= $index) {
                $left[] = $arr[$k];
            } else {
                $right[] = $arr[$k];
            }
        }
        $left  = self::sort($left);
        $right = self::sort($right);
        return array_merge($left, [$index], $right);
    }
}
```

# 选择排序

```
<?php
/**
 * 时间复杂度 O(n²)
 * 空间复杂度 O(1)
 */
class SelectSort
{
    public static function sort($data) {
        $length = count($data);
        if ($length <= 1) {
            return $data;
        }
        for ($i=0; $i < $length - 1; $i++) {
            $min = $i;
            for ($k=$i + 1; $k < $length; $k++) { 
                if ($data[$k] < $data[$min]) {
                    $min = $k;
                }
            }
            if ($i != $min) {
                $tmp = $data[$i];
                $data[$i] = $data[$min];
                $data[$min] = $tmp;
            }
        }
        return $data;
    }    
}
```

# 素数判断

```
<?php

function isPrime($number)
{
    if ($number <= 3) {
        return $number > 1;
    }

    $square = ceil(sqrt($number));
    for ($i=2; $i < $square; $i++) { 
        if (0 == $number % $i) {
            return false;
        }
    }
    return true;
}
```

# 判断链表是否存在环

```
<?php

class Node
{
    public function __construct($data)
    {
        $this->data = $data;
    }

    public $data = null;

    public $next = null; 
}

function checkList(Node $node)
{
    $fast = $slow = $node;
    while (true) {
        if (null !== $fast->next && null !== $fast->next->next) {
            $fast = $fast->next->next;
            $slow = $slow->next;
        } else {
            return false;
        }

        if ($fast === $slow) {
            $p1 = $node;
            $p2 = $fast;
            while ($p1 != $p2) {
                $p1 = $p1->next;
                $p2 = $p2->next;
            }
            return $p1;
        }
    }
}
```



