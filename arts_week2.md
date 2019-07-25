
``` java

//jdk 7 操作
Map<String, List<Student>> stuMap = new HashMap<String, List<Student>>();
        for (Student stu: studentsList) {
            if (stu.getHeight() > 160) { // 如果身高大于 160
                if (stuMap.get(stu.getSex()) == null) { // 该性别还没分类
                    List<Student> list = new ArrayList<Student>(); // 新建该性别学生的列表
                    list.add(stu);// 将学生放进去列表
                    stuMap.put(stu.getSex(), list);// 将列表放到 map 中
                } else { // 该性别分类已存在
                    stuMap.get(stu.getSex()).add(stu);// 该性别分类已存在，则直接放进去即可
                }
            }
        }

//jdk 8 增加两个流方法
//串行
Map<String, List<Student>> stuMap = stuList.stream().filter((Student s) -> s.getHeight() > 160) .collect(Collectors.groupingBy(Student ::getSex)); 

//并行
Map<String, List<Student>> stuMap = stuList.parallelStream().filter((Student s) -> s.getHeight() > 160) .collect(Collectors.groupingBy(Student ::getSex)); 


```
