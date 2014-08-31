---
layout: post
category : datastruts
date: 2013/01/01 03:00:00 
title: 排序之插入排序
tags : datastruts
---
{% include JB/setup %}

##概述
插入排序包括 直接插入排序和希尔排序 直接插入排序的基本操作是：将一个记录插入到已排好序的有序列表中，从而得到一个新的，记录数增1的有序表

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

<pre class="brush: java;">

package cn.liuyiyou.sort;

/**
 * 插入排序：插入排序包括 直接插入排序和希尔排序
 直接插入排序的基本操作是：将一个记录插入到已排好序的有序列表中，从而得到一个新的，记录数增1的有序表。
 * User: liuyiyou
 * Date: 14-8-25
 * Time: 下午5:14
 */
public class InsertSort {

    public static void insertSort1(int[] array){

        //从位置1开始，0做为哨兵
        for (int i = 1 ; i < array.length; i++){
            //保存当前位置到临时变量
            int temp = array[i];
            //将当前位置赋给紧邻的未排序的起点
            int j = i;
            //如果未排序的数小于已排序的树
            if (array[i-1]>array[i]){
                //向后移动一个位置
                while (j>0 && array[j-1]> temp){     //只能是temp，不能是array[i] ,如果是array[i],在执行下面这句话的时候，array[i]会改的值被改变了
                    array[j] = array[j-1];
                    j--;
                }
                //将当前位置存放为i
                array[j] = temp;    //同上，比如i为1的时候array[i]=3，array[j-1]为1，执行while里面的前移之后，array[j]=array[i]=3.而实际的情况应该是array[j]
            } else {
                System.out.println(i);
            }

        }
    }

    public static void insertSort2(int[] array) {
        for (int i = 1; i < array.length; i++) {
            int temp = array[i];  //这个可以放在if里面
            int j = i;
            if (array[i] <array[i - 1] ) {
                for ( ; j > 0 && temp < array[j - 1] ; j--) {
                    array[j] = array[j - 1];
                }
                array[j] = temp;
            }
        }
    }


    //这个是错误的
    public static void insertBadSort1(int  [] array){

        for(int i=1; i < array.length;i++){

            if(array[i] < array[i-1]){
                array[0]= array[i];//先保存一下后面的那个

                array[i]=array[i-1];// 前面的那个后移。

                int insertPos=0;

                for(int k= i-1;k>=0;k--){

                    if(array[k]>array[0]){

                        array[k+1]=array[k];

                    }else{

                        insertPos=k+1;

                        break;

                    }

                }

                array[insertPos]=array[0];

            }

        }

    }





    //这个是错误的
    public static void insertBadSort2(int[] array){

        for (int i = 1 ; i < array.length; i++){
            int temp = array[i];
            int j = i;
            if (array[j-1]>temp){ //错误，a[j-1]>temp 即 a[i-1]>a[i] ，这个每次从外层循环都会成立，应该是a[i-1]>a[i]
                while (j>=1 && (array[j-1]>temp)){
                    array[j] = array[j-1];
                    j--;
                }
            }

        }
    }


    public static void main(String[] args) {

        int [] array = SortUtil.array;
        System.out.println("原始排序结果：");
        SortUtil.print(array);
        insertSort1(array);
        System.out.println("最终排序结果：");
        SortUtil.print(array);

    }
}


</pre>