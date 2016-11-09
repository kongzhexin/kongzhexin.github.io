---
title: sort
---
1. 冒泡排序 
所有元素循环一遍
```
function bublesort(arr) {
    var len=arr.length;
    var temp;
    for(var i=0;ilen;i++){
        for(var j=0;jlen-1-i;j++){
            if(arr[j]arr[j+1]){    
                temp=arr[j];
                arr[j]=arr[j+1];
                arr[j+1]=temp;

            }
        }
    }
    return arr;
}
```
2. 插入排序：扑克牌 和冒泡很类似
```
function insertSort(arr) {
    var len =arr.length;
    var temp;
    for(var i=1;ilen;i++){
        while(arr[i]arr[i-1]&&i-1=0){
            temp=arr[i];
            arr[i]=arr[i-1];
            arr[i-1]=temp;
            i--;
        }
    }
    return arr;
}
```
3. 选择排序
每次选择最小的，默认第一个最小，比较保存最小索引 和第一个交换 
```
function selectSort(arr) {
    var len =arr.length;
    var  minIndex;
    var temp;

    for(var i=0;ilen;i++){
        minIndex=i;
        for(var j=i+1;jlen;j++){
            if(arr[minIndex]arr[j]){
                minIndex=j;
            }
        }
        temp =arr[i];
        arr[i]= arr[minIndex];
        arr[minIndex]=temp;
    }
    return arr;
}
```
4. 希尔排序
间隔插入排序  当间隔为1时 完成排序
```
function shellSort(arr,d) {
    var len =arr.length;
    var temp;
    while(true){
        for(var i=0;id;i++){
            for(var j=i;j+dlen;j+=d){
                if(arr[j]arr[j+d]){
                    temp=arr[j];
                    arr[j]=arr[d+j];
                    arr[j+d]=temp;
                }

            }
        }
        if(d==1) break;
        d--;
    }
    return arr;
}
```
5. 归并排序
分而治之

6. 快速排序
默认第一个元素 和之后元素比较从高位开始，比它大则继续比较，比它小则给low位赋值；
                             然后从低位开始，比它小则继续比较，比它大则给high赋值
                            最后：v[low]=key
```
function quickSort(v,left,right) {
    if(left  right){
        var key = v[left];
        var low = left;
        var high = right;
        while(low  high){
            while(low  high && v[high]  key){
                high--;
            }
            v[low] = v[high];
            while(low  high && v[low]  key){
                low++;
            }
            v[high] = v[low];
        }
        v[low] = key;
        quickSort(v,left,low-1);
        quickSort(v,low+1,right);
    }
}
```
