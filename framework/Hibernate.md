# 注解

## @MappedSuperclass

最近在进行开发时，阅读别人的代码，我发现了一个名为@MappedSuperclass的注解，基于自己首次见的原因，上网查询了一下其的意义，在此做个总结,以便以后可以回顾整理。

使用条件：

当我们进行开发项目时，我们经常会用到实体映射到数据库表的操作，此时我们经常会发现在我们需要映射的几个实体类中，有几个共同的属性，例如编号ID，创建者，创建时间，修改者，修改时间，备注等。遇到这种情况，我们可能会想到把这些属性抽象出来当成一个父类，然后再以不同的实体类来继承这个父类。

那么，我们便可以使用@MappedSuperclass注解，通过这个注解，我们可以将该实体类当成基类实体，它不会映射到数据库表，但继承它的子类实体在映射时会自动扫描该基类实体的映射属性，添加到子类实体的对应数据库表中。
使用环境：

1.@MappedSuperclass注解使用在父类上面，是用来标识父类的作用

2.@MappedSuperclass标识的类表示其不能映射到数据库表，因为其不是一个完整的实体类，但是它所拥有的属性能够映射在     其子类对用的数据库表中

3.@MappedSuperclass标识得类不能再有@Entity或@Table注解  但是可以使用@Id 和@Column注解

实际例子：

父类：

```java
import java.io.Serializable;
import java.util.Date;
import javax.persistence.Column;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.MappedSuperclass;
import javax.persistence.Temporal;
import javax.persistence.TemporalType;
import javax.persistence.Version;

@MappedSuperclass
public abstract class BaseDomain implements Serializable {
    private static final long serialVersionUID = 6786042125997164613L;
    @Id
    @GeneratedValue(
        strategy = GenerationType.IDENTITY
    )
    @Column(
        name = "ID"
    )
    private Long id;
    @Column(
        name = "CreatedTime",
        nullable = true
    )
    @Temporal(TemporalType.TIMESTAMP)
    private Date createdTime = new Date();
    @Column(
        name = "CreatedBy",
        length = 50,
        nullable = true
    )
    private String createdBy;
    @Column(
        name = "UpdatedTime",
        nullable = true
    )
    @Temporal(TemporalType.TIMESTAMP)
    private Date updatedTime = new Date();
    @Column(
        name = "UpdatedBy",
        length = 50,
        nullable = true
    )
    private String updatedBy;
    @Column(
        name = "Version",
        nullable = false
    )
    @Version
    private int version;

    public BaseDomain() {
    }
}

```

子类：

```java
@Entity
@Table(name = "report")
public class Report extends BaseDomain{
    private static final long serialVersionUID = 1L;
    @Column(name = "driverId", length = 20)
    private Long driverId;//报备司机编码
    
    @Column(name = "driverType", length = 4)
    private Integer driverType;//司机类型，driverType枚举
    
    @Column(name = "fleetManageId", length = 20)
    private Long fleetManageId;// 归属车队
}
```

