---
layout: post
title: Java注解基本知识
date: 2017-10-23
categories:
- tool
tags: [Java, 注解]
status: publish
type: post
published: true
meta:
  _edit_last: '1'
  views: '2'
author:
  login: slayer
  email: 18723126233@163.com
  display_name: jiangtaibao
---

## 定义：
为代码提供一种形式化（元数据+源代码）的方法，为我们在其他地方可以方便的使用。元数据就是描述数据的数据，就是描述源代码的
## 为什么引入注解：
第一个原因：假如你想为应用设置很多的常量或参数，最好使用XML，因为它不会同特定的代码相连。如果你想把某个方法声明为服务，那么使用Annotation会更好一些，因为这种情况下需要注解和方法紧密耦合起来。
第二个原因：Annotation定义了一种标准的描述元数据的方式
## JAVA注解的ORM框架
![161325133151441](https://user-images.githubusercontent.com/14925803/29908475-27c394d2-8e54-11e7-8d4c-8514b7ee0b97.jpg)
## JAVA注解的实例
/***********注解声明***************/

     /**水果名称**/
    @Target(ElementType.FIELD)
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    public @interface FruitName {
        String value() default "";
       }

     /**水果颜色注解*/
    @Target(ElementType.FIELD)
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    public @interface FruitColor {
    /**颜色枚举 */
    public enum Color{ BULE,RED,GREEN};
    /**颜色属性@return  */
    Color fruitColor() default Color.GREEN;
    }

    /* * 水果供应者注解**/
    @Target(ElementType.FIELD)
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    public @interface FruitProvider {
     /** 供应商编号@return*/
     public int id() default -1;
     /** 供应商名称 @return */
     public String name() default "";
     /** 供应商地址 @return*/
     public String address() default "";
    }

    /***********注解使用***************/

     public class Apple {
    
    @FruitName("Apple")
    private String appleName;
    
    @FruitColor(fruitColor=Color.RED)
    private String appleColor;
    
    @FruitProvider(id=1,name="陕西红富士集团",address="陕西省西安市延安路89号红富士大厦")
    private String appleProvider;
    
    public void setAppleColor(String appleColor) {
        this.appleColor = appleColor;
    }
    public String getAppleColor() {
        return appleColor;
    }
    
    public void setAppleName(String appleName) {
        this.appleName = appleName;
    }
    public String getAppleName() {
        return appleName;
    }
    
    public void setAppleProvider(String appleProvider) {
        this.appleProvider = appleProvider;
    }
    public String getAppleProvider() {
        return appleProvider;
    }
    
    public void displayName(){
        System.out.println("水果的名字是：苹果");
    }
   }

    /***********注解处理器***************/

    public class FruitInfoUtil {
    public static void getFruitInfo(Class<?> clazz){
        
        String strFruitName=" 水果名称：";
        String strFruitColor=" 水果颜色：";
        String strFruitProvicer="供应商信息：";
        
        Field[] fields = clazz.getDeclaredFields();
        
        for(Field field :fields){
            if(field.isAnnotationPresent(FruitName.class)){
                FruitName fruitName = (FruitName) field.getAnnotation(FruitName.class);
                strFruitName=strFruitName+fruitName.value();
                System.out.println(strFruitName);
            }
            else if(field.isAnnotationPresent(FruitColor.class)){
                FruitColor fruitColor= (FruitColor) field.getAnnotation(FruitColor.class);
                strFruitColor=strFruitColor+fruitColor.fruitColor().toString();
                System.out.println(strFruitColor);
            }
            else if(field.isAnnotationPresent(FruitProvider.class)){
                FruitProvider fruitProvider= (FruitProvider) field.getAnnotation(FruitProvider.class);
                strFruitProvicer=" 供应商编号："+fruitProvider.id()+" 供应商名称："+fruitProvider.name()+" 供应商地址："+fruitProvider.address();
                System.out.println(strFruitProvicer);
            }
        }
    }
    }

    /***********输出结果***************/
    public class FruitRun {

    /**
     * @param args
     */
    public static void main(String[] args) {
        
        FruitInfoUtil.getFruitInfo(Apple.class);
        
      }

    }
   ====================================
   水果名称：Apple
   水果颜色：RED
   供应商编号：1 供应商名称：陕西红富士集团 供应商地址：陕西省西安市延安路89号红富士大厦


