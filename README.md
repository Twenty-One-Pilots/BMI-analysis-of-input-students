# BMI-analysis-of-input-students
BMI analysis of input students，by Java.

今天的Java实验，是上一次实验代码的改编。要求如下：
> 1）将 OOBMI改造为CollectionBMI类；将CollectionBMI中的Student[] students改造为ArrayList&lt;Student> students； 
2）改造inputStudents函数和相关的isExists函数，不要求用户输入学生人数，通过询问用户是否继续输入来决定是否继续输入学生，并判断输入的学生是否已经存在，如果不存在则将输入的学生对象保存到ArrayList&lt;Student> students中。
3）增加五个comparator子类(内部类)，能够利用Collections.sort函数对学生分别按照学号、姓名、身高、体重、BMI进行排序。
4）改造printStatics函数，该函数可以打印所有学生基本信息（利用增强for语句），以及统计结果信息。打印时，每个学生的信息打印为一行，为了清晰，学号、姓名、身高、体重和计算后的bmi值之间用制表符(\t)隔开；打印完学生信息后，打印BMI统计信息。
5）在CollectionBMI的main函数中，调用上述函数，完成学生信息生成、按不同属性排序、结果输出的完整过程。

可见第三题是难点，需要引入接口、继承等概念。而且输入结束的标志处也应多加斟酌。代码如下：
``` java
package oobmi;

import java.math.BigDecimal;
import java.util.ArrayList;
import java.util.Scanner;
import java.util.Collections;
import java.util.Comparator;

public class CollectionBMI {

    int i, j, k = 0;                //定义循环变量
    ArrayList<Student> students = new ArrayList<Student>();

    public static void main(String[] args) {
        CollectionBMI s = new CollectionBMI();
        s.inputStudents();
        s.printStatics();
    }

    public void inputStudents() {
        Scanner in = new Scanner(System.in);
        System.out.println("请输入学生信息；输入结束后以空格回车完成输入。");
        String flag = "0";
        while (flag.compareTo(" ") != 0) {            //设定循环结束条件
            Student stu = new Student("", "", 0, 0, 0);
            stu.id = in.next();
            stu.name = in.next();
            stu.height = in.nextDouble();
            stu.weight = in.nextDouble();
            if (isExists(stu.id)) {
                System.out.println("学号重复，请重新输入:");
                continue;
            }
            students.add(stu);
            flag = in.nextLine();

        }
    }

    public boolean isExists(String id) {
        for (Student s : students) {            //判断该ID是否出现过
            if (id.equals(s.id)) {
                return true;
            }
        }
        return false;
    }

    public double ave(ArrayList<Student> students) {
        double ave = 0;
        for (i = 0; i < students.size(); i++) {         //计算bmi总和
            ave += students.get(i).bmi;
        }
        ave /= students.size();                                     //计算平均数
        return ave;
    }

    public double med(ArrayList<Student> students) {
        int i, j;
        double temp;
        Student TempArray[] = students.toArray(new Student[students.size()]);//;
        //System.arraycopy(students, 0, TempArray, 0, students.size());
        for (i = 0; i < students.size(); i++) {                       //将bmi排序
            for (j = i + 1; j < students.size(); j++) {
                if (TempArray[j].bmi < TempArray[i].bmi) {
                    temp = TempArray[j].bmi;
                    TempArray[j].bmi = TempArray[i].bmi;
                    TempArray[i].bmi = temp;
                }
            }
        }
        if (students.size() % 2 == 0) {                         //就奇偶个学生分类
            return (TempArray[students.size() / 2].bmi + TempArray[students.size() / 2 - 1].bmi) / 2;
        } else {
            return TempArray[(students.size() - 1) / 2].bmi;
        }
    }

    public double[] mod(ArrayList<Student> students) {
        int times[] = new int[students.size()];         //定义每一个数值在其后出现的次数构成的数组
        double mode[] = new double[students.size()];//定义众数和0构成的数组
        int max = 0;                            //定义最大的次数
        for (i = 0; i < students.size(); i++) {
            for (j = i; j < students.size(); j++) {
                if (students.get(i).bmi == students.get(j).bmi) {
                    times[i]++;                 //对数组times[]赋值
                }
            }
        }
        max = times[0];
        for (i = 0; i < students.size(); i++) {            //使max的值为最大次数
            if (max <= times[i]) {
                max = times[i];
            }
        }
        for (i = 0; i < students.size(); i++) {
            if (max == times[i]) {
                mode[i] = students.get(i).bmi;      //对数组mode[]赋值
            }
        }
        return mode;                        //返回mod[]
    }

    public double var(ArrayList<Student> students) {
        double sum = 0;
        for (i = 0; i < students.size(); i++) {
            sum += students.get(i).bmi;
        }
        double ave = sum / students.size();             //计算平均值
        double var = 0;
        for (i = 0; i < students.size(); i++) {
            var += (students.get(i).bmi - ave) * (students.get(i).bmi - ave);       //方差的计算
        }
        var /= students.size();
        return var;                        //返回方差
    }

    private class IDComparator implements Comparator<Student> {//对ID排序

        @Override
        public int compare(Student st1, Student st2) {
            if (st1.id.compareTo(st2.id) < 0) {
                return 1;
            } else if (st1.id.compareTo(st2.id) > 0) {
                return -1;
            }
            return 0;
        }
    }

    private class NameComparator implements Comparator<Student> {   //对姓名排序

        @Override
        public int compare(Student st1, Student st2) {
            if (st1.name.compareTo(st2.name) < 0) {
                return 1;
            } else if (st1.name.compareTo(st2.name) > 0) {
                return -1;
            }
            return 0;
        }
    }

    private class HeightComparator implements Comparator<Student> { //对身高排序

        @Override
        public int compare(Student st1, Student st2) {
            if (st1.height < st2.height) {
                return 1;
            } else if (st1.height > st2.height) {
                return -1;
            }
            return 0;
        }
    }

    private class WeightComparator implements Comparator<Student> { //对体重排序

        @Override
        public int compare(Student st1, Student st2) {
            if (st1.weight < st2.weight) {
                return 1;
            } else if (st1.weight > st2.weight) {
                return -1;
            }
            return 0;
        }
    }

    private class BMIComparator implements Comparator<Student> {    //对BMI排序

        @Override
        public int compare(Student st1, Student st2) {
            if (st1.bmi < st2.bmi) {
                return 1;
            } else if (st1.bmi > st2.bmi) {
                return -1;
            }
            return 0;
        }
    }

    public void printStatics() {
        System.out.println("id\t姓名\t身高\t体重\tbmi");          //输出表头
        for (Student s : students) {
            System.out.println(s.toString());         //输出学生信息
        }

        CollectionBMI bmi = new CollectionBMI();
        Collections.sort(students, bmi.new IDComparator().reversed());
        System.out.println("按照id排序后：");
        System.out.println("id\t姓名\t身高\t体重\tbmi");          //输出表头
        for (Student s : students) {
            System.out.println(s.toString());         //输出学生信息
        }

        Collections.sort(students, bmi.new NameComparator().reversed());
        System.out.println("按照姓名排序后：");
        System.out.println("id\t姓名\t身高\t体重\tbmi");          //输出表头
        for (Student s : students) {
            System.out.println(s.toString());         //输出学生信息
        }

        Collections.sort(students, bmi.new HeightComparator().reversed());
        System.out.println("按照身高排序后：");
        System.out.println("id\t姓名\t身高\t体重\tbmi");          //输出表头
        for (Student s : students) {
            System.out.println(s.toString());         //输出学生信息
        }

        Collections.sort(students, bmi.new WeightComparator().reversed());
        System.out.println("按照体重排序后：");
        System.out.println("id\t姓名\t身高\t体重\tbmi");          //输出表头
        for (Student s : students) {
            System.out.println(s.toString());         //输出学生信息
        }

        Collections.sort(students, bmi.new BMIComparator().reversed());
        System.out.println("按照bmi排序后：");
        System.out.println("id\t姓名\t身高\t体重\tbmi");          //输出表头
        for (Student s : students) {
            System.out.println(s.toString());         //输出学生信息
        }

        System.out.println("bmi的平均数为 " + String.format("%.2f", ave(students)) + ";");
        System.out.println("bmi的中位数为 " + String.format("%.2f", med(students)) + ";");
        String str = "";                                                    //创建众数构成的字符串
        for (k = 0; k < students.size(); k++) {                               //注意使用j以与mod()中的循环变量i,j区分
            if (mod(students)[k] != 0) {
                str = str + " " + mod(students)[k];
            }
        }
        System.out.println("bmi的众数为" + str + ";");
        System.out.println("bmi的方差为 " + String.format("%.2f", var(students)) + ".");
    }

}

class Student {

    String id;              //定义学生id
    String name;        //定义学生姓名
    double height;      //定义学生身高
    double weight;      //定义学生体重
    double bmi;           //定义bmi

    public String toString() {
        bmi = weight / height / height;     //计算bmi
        BigDecimal bm = new BigDecimal(bmi);
        bmi = bm.setScale(2, BigDecimal.ROUND_HALF_UP).doubleValue();//对bmi四舍五入保留两位小数
        return id + "\t" + name + "\t" + height + "\t" + weight + "\t" + bmi;
    }

    public Student(String id, String name, double height, double weight, double bmi) {//定义创建函数
        this.id = id;                                           //赋值
        this.name = name;
        this.weight = weight;
        this.height = height;
        this.bmi = bmi;
    }
}

```
 <center> ![这里写图片描述](https://img-blog.csdn.net/20180614215510304?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3R3ZW50eW9uZXBpbG90cw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
 ![这里写图片描述](https://img-blog.csdn.net/20180614215517497?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3R3ZW50eW9uZXBpbG90cw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
<center>测试结果</center>
