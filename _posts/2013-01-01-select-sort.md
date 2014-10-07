---
layout: post
category : datastruts
date: 2013/01/01 01:00:00 
title: 排序之选择排序
tags : datastruts
---


##概述
选择排序：每一趟从待排序的数据元素中选出最小（或最大）的一个元素，顺序放在已排好序的数列的最后，直到全部待排序的数据元素排完。 选择排序是不稳定的排序方法

##算法原理
冒泡排序算法的运作如下：（从后往前）

- 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
- 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。在这一点，最后的元素应该会是最大的数。
- 针对所有的元素重复以上的步骤，除了最后一个。
- 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较

##算法分析

###时间复杂度

若文件的初始状态是正序的，一趟扫描即可完成排序。所需的关键字比较次数$$C$$和记录移动次数$$M$$均达到最小值:$$C_{min}=n-1$$,$$M_{min}=0$$。所以，冒泡排序最好的时间复杂度为$$O(n)$$ 。

若初始文件是反序的，需要进行$$n-1$$趟排序。每趟排序要进行$$n-i$$次关键字的比较($$1≤i≤n-1$$),且每次比较都必须移动记录三次来达到交换记录位置。在这种情况下，比较和移动次数均达到最大值：

$$C_{max} = \left(n(n-1)\over 2 \right) = O(n^2)$$

$$M_{max} = \frac{3n(n-1)}{2} = O(n^2)$$

冒泡排序的最坏时间复杂度为$$O(n^2)$$ 。

综上，因此冒泡排序总的平均时间复杂度为$$O(n^2)$$ 

###算法稳定性

冒泡排序就是把小的元素往前调或者把大的元素往后调。比较是相邻的两个元素比较，交换也发生在这两个元素之间。所以，如果两个元素相等，我想你是不会再无聊地把他们俩交换一下的；如果两个相等的元素没有相邻，那么即使通过前面的两两交换把两个相邻起来，这时候也不会交换，所以相同元素的前后顺序并没有改变，所以冒泡排序是一种稳定排序算法。

###算法描述


{% highlight java %}

package cn.liuyiyou.sort;

/**
 * 每一趟从待排序的数据元素中选出最小（或最大）的一个元素，顺序放在已排好序的数列的最后，直到全部待排序的数据元素排完。 选择排序是不稳定的排序方法。
 * User: liuyiyou
 * Date: 13-1-1
 * Time: 下午5:28
 */
public class SelectSort {


    //每一趟从待排序的数据元素中选出最小（或最大）的一个元素，顺序放在已排好序的数列的最后，直到全部待排序的数据元素排完。 选择排序是不稳定的排序方法。
    public static void selectSort(int[] array) {
        if ((array == null) || (array.length == 0)) return;
        int minIndex = 0;
        for (int i = 0; i < array.length; i++) {
            minIndex = i;
            for (int j = i + 1; j < array.length; j++) {
                if (array[minIndex] > array[j]) {
                    minIndex = j;
                }
            }
            SortUtil.swap(array,i, minIndex);
            SortUtil.print(array,i);
        }
    }


    //这个就是稳定算法了
    public static void selectSort2(int array[]) {
        int minIndex = 0;
        if ((array == null) || (array.length == 0)) return;
        for (int i = 0; i < array.length; i++) {
            minIndex = i;//无序区的最小数据数组下标
            for (int j = i + 1; j < array.length; j++) {//在无序区中找到最小数据并保存其数组下标
                if (array[j] < array[minIndex]) {
                    minIndex = j;
                }
            }
            if (minIndex != i) {//如果不是无序区的最小值位置且不是默认的第一个数据，则交换之。
                SortUtil.swap(array,i,minIndex);
            }
            SortUtil.print(array,i);
        }
    }


    public static void main(String[] args) {

        int [] array = SortUtil.array;
        System.out.println("原始排序结果：");
        SortUtil.print(array);
        selectSort2(array);
        System.out.println("最终排序结果：");
        SortUtil.print(array);

    }
}

{% endhighlight %}

