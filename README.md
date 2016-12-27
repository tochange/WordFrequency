# WordFrequency——计算一篇文章中出现最多的五个单词次数

① 单词不区分大小写,比如：The和the是相同的单词.
② 按照英文书写习惯,过长的单词遇到换行时,会加入连字符“-”.比如：“international”遇到换行时可能会写为“intern-ational”,统计时要注意除去连字符.



    private void heapify(int i) {
        // 获取左右结点的数组下标
        int l = left(i);
        int r = right(i);

        // 这是一个临时变量，表示 跟结点、左结点、右结点中最小的值的结点的下标
        int smallest = i;

        // 存在左结点，且左结点的值小于根结点的值
        if (l < data.length && data[l] < data[i])
            smallest = l;

        // 存在右结点，且右结点的值小于以上比较的较小值
        if (r < data.length && data[r] < data[smallest])
            smallest = r;

        // 左右结点的值都大于根节点，直接return，不做任何操作
        if (i == smallest)
            return;

        // 交换根节点和左右结点中最小的那个值，把根节点的值替换下去
        swap(i, smallest);

        // 由于替换后左右子树会被影响，所以要对受影响的子树再进行heapify
        heapify(smallest);
    }
    
    
    
package com.example;

import java.io.BufferedReader;
import java.io.File;
import java.io.FileReader;
import java.util.Collections;
import java.util.Comparator;
import java.util.HashMap;
import java.util.LinkedList;
import java.util.List;
import java.util.Map;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class JavaTest<T> {
    public static void main(String[] args) {
        System.out.println("main() called");
        for (int i = 0; i < 1; i++) {
            try {
                long beging = System.currentTimeMillis();
                count1();
                print("cost:(in ms)" + (System.currentTimeMillis() - beging));
            } catch (Exception e) {
                print(e.toString());
                e.printStackTrace();
            }
        }
    }

    static class MyString {
        public String string;

        public MyString(String string) {
            this.string = string;
        }

        @Override
        public String toString() {
            return string;
        }

        @Override
        public int hashCode() {
            return string.toLowerCase().hashCode();
        }

        @Override
        public boolean equals(Object anObject) {
            if (this == anObject) {
                return true;
            }
            if (anObject instanceof MyString) {
                MyString anotherString = (MyString) anObject;
                int n = string.length();
                if (n == anotherString.string.length()) {
                    int i = 0;
                    while (n-- != 0) {
                        char c1 = string.charAt(i);
                        char c2 = anotherString.string.charAt(i);
                        if (!(c1 == c2 || (Math.abs(c1 - c2) == 32)))
                            return false;
                        i++;
                    }
                    return true;
                }
            }
            return false;
        }
    }

    public static void count1() throws Exception {
        Runtime run = Runtime.getRuntime();
        run.gc();
        long startMem = run.totalMemory() - run.freeMemory();
        System.out.println("memory> total:" + run.totalMemory() + " free:" + run.freeMemory() + " used:" + startMem);

        Pattern pattern = Pattern.compile("[a-zA-Z]+");
//        Pattern pattern = Pattern.compile("^[a-zA-Z]\\\\w*");
        String line;
        Map<MyString, Integer> map = new HashMap<MyString, Integer>();
        String lastLineUnFinishWord = "";
        String rawWord;
        boolean isFirstWordOfLine;
        boolean unFinish;
        FileReader fileReader = null;
        BufferedReader reader = null;
        try {
            File file = new File("/Users/joey/Desktop/wordFrequencyTest");
            fileReader = new FileReader(file);
            reader = new BufferedReader(fileReader);

            while ((line = reader.readLine()) != null) {
                Matcher matcher = pattern.matcher(line);
                unFinish = line.endsWith("-");
                isFirstWordOfLine = true;

                while (matcher.find()) {
                    rawWord = matcher.group();
                    if (isFirstWordOfLine && !"".equals(lastLineUnFinishWord)) {
                        if (Character.isLetter(line.charAt(0))) {
                            if (matcher.end() == line.length() - 1) {
                                lastLineUnFinishWord = lastLineUnFinishWord + rawWord;
                                break;
                            } else {
                                rawWord = lastLineUnFinishWord + rawWord;
                                lastLineUnFinishWord = "";
                            }
                        } else {// regard as finish
                            putMap(lastLineUnFinishWord, map);
                        }

                    }
                    isFirstWordOfLine = false;

                    if (unFinish && matcher.end() == line.length() - 1) {
                        lastLineUnFinishWord = rawWord;
                        break;
                    }
                    putMap(rawWord, map);
                }
            }
        } finally {
            if (reader != null)
                reader.close();
            if (fileReader != null)
                fileReader.close();
        }

        List<Map.Entry<MyString, Integer>> list = new LinkedList<Map.Entry<MyString, Integer>>(
                map.entrySet());// put Entry to List
        Compare compare = new Compare();//rewrite Comparator
        for (int i = 0; i < 5; i++) {
            Map.Entry<MyString, Integer> entry = Collections.max(list, compare);// max
            MyString key = entry.getKey();
            Integer value = entry.getValue();
            int index = list.indexOf(entry);//get max's index
            System.out.println(key + " " + value);
            list.remove(index);//remove max
        }

        long endMem = run.totalMemory() - run.freeMemory();
        System.out.println("memory> total:" + run.totalMemory() + " free:" + run.freeMemory() + " used:" + endMem);
        System.out.println("memory> coast:" + (endMem - startMem));
    }

    private static void putMap(String rawWord, Map<MyString, Integer> map) {
        MyString word = new MyString(rawWord);
        if (map.containsKey(word)) {
            int times = map.get(word);
            map.put(word, times + 1);// save old key
        } else {
            map.put(word, 1);
        }
    }

    static class Compare implements Comparator<Map.Entry<MyString, Integer>> {
        public int compare(Map.Entry<MyString, Integer> left, Map.Entry<MyString, Integer> right) {
            return left.getValue().compareTo(right.getValue());
        }
    }

    static void print(String s) {
        System.out.println(s);

    }

}
