---
title: 工厂模式（Factory Pattern）
date: 2023-11-22
category:
  - 设计模式(Pattern)
tag:
  - java
sticky: false
---

# Design Patterns Factory



# 一、创建型模式：

## 2、工厂模式

### 简单工厂

![](https://kroki.io/plantuml/svg/eNqlU81KAzEQvucp5liRBi966qGi9AFsb-JhSKZx2PyZTBVp67O73S3SQ-mu9BLCwPc7ybwKFtkEr2rDMWPBAAVjUynD_d3J0GaGh9NBRtOgoxWLp0fPLgaKAp7WopTxWCuYFLRQFW2wUtUhWfJ61Q6eKSTYqlvYturCZg-fiS0E5DhZSuHoXt9u1F5xFCprNHSeqr5jJr08nC3bT09iC35NDuALHnrgExfjqfMxGtmJLdBIKt8dsld3JN3l6H6U_AsZwej-6-AY-mOD5Ryy7X6oqtluOh3u5Xqev4DXU_Vx1eUHNdtpPby1MWbG8ICaU7Ttx_kFgWYoOQ==)
```markdown
─model
    │  ShapeFactory.java
    │  TestDemo.java
    │
    └─shape
            Circle.java
            Rectangle.java
            Shape.java
            Square.java
```

public interface Shape

```markdown
package com.test.cases.model.shape;

public interface Shape {
    void draw();
}
```

public class Square implements Shape

```markdown
package com.test.cases.model.shape;

public class Square implements Shape{
    @Override
    public void draw() {
        System.out.println("Shape Square draw");
    }
}
```

public class Rectangle implements Shape

```markdown
package com.test.cases.model.shape;

public class Rectangle implements Shape {
    @Override
    public void draw() {
        System.out.println("Shape Rectangle draw");
    }
}
```

public class Circle implements Shape

```markdown
package com.test.cases.model.shape;

public class Circle implements Shape{
    @Override
    public void draw() {
        System.out.println("Shape Circle draw");
    }
}
```

public class ShapeFactory

```markdown
package com.test.cases.model;

import com.test.cases.model.shape.Circle;
import com.test.cases.model.shape.Rectangle;
import com.test.cases.model.shape.Shape;
import com.test.cases.model.shape.Square;

public class ShapeFactory {
    public Shape getShape(String shapeType){
        if(shapeType == null){
            return null;
        }
        if(shapeType.equalsIgnoreCase("CIRCLE")){
            return new Circle();
        } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
            return new Rectangle();
        } else if(shapeType.equalsIgnoreCase("SQUARE")){
            return new Square();
        }
        return null;
    }
}
```

public class TestDemo

```markdown
package com.test.cases.model;

import com.test.cases.model.shape.Shape;

public class TestDemo {
    public static void main(String[] args) {
        ShapeFactory shapeFactory = new ShapeFactory();

        //获取 Circle 的对象，并调用它的 draw 方法
        Shape circle = shapeFactory.getShape("CIRCLE");
        circle.draw();

        //获取 Rectangle 的对象，并调用它的 draw 方法
        Shape rectangle = shapeFactory.getShape("RECTANGLE");
        rectangle.draw();

        //获取 Square 的对象，并调用它的 draw 方法
        Shape square = shapeFactory.getShape("SQUARE");
        square.draw();
    }
}
```

### 抽象工厂

![](https://kroki.io/plantuml/svg/eNq1VkFOwzAQvPsVPoKq-AUcCkVwRS03hJBxtsGSa5e1A0JteTuOnVRpRaXYUS6JlWxmZse768yt4-jqjSJSO8A1F0ANVqwEKyv9tuXOP9WW8XfrkAvnA5zBHyaMMsgWzZXuyC_9MrKka6nU1TU5EKG4tQk4d6oGDzPLhnkG6-5hYwLIzufkpDhEtA2X-mrlUOrq5TUN9SHen9CUtQA8Bb9tg9sgWkG3bNlyjFhCOcqH1QffQqeoAQoPGm1hcVQ2o3Hr_Iuw6EnuIOlw1nMrgk9d0OECUz_kgsqUzG3zJVtIFKpXSiXy70QLg8pxFqa1UlQeWY6tlCE8VtAjAuhRNRT1LEE4rquRZra5fdYc_wPKKa6bfVEk98K0TP2SyWMq6m2RM5JIzhgbznYcq4SkHgrDSZLdO6HZM5Z40EzN4Wf41BShyYftSX-6DN-T5N45oRmazNmkmZoojqGpWeIRRMgcdOn_q_4AyO2Gqg==)

