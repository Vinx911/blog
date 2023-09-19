---
title: Jetpack架构组件之Room
date: 2022-11-04 18:58:06
id: 38
tags: Jetpack
categories: Jetpack架构组件
copyright: true
---

## Entity(实体)

‌‌‌　　一个 Entity 对应于数据库中的一张表。Entity 类是 Sqlite 表结构对 Java/kotlin 的映射，在 Java 中可以看作一个 Model 类。

### Entity(注解实体类)

```kotlin
‌‌‌　　@Entity(tableName = "word_table")
‌‌‌　　class Word(@PrimaryKey @ColumnInfo(name = "word") val word: String)
```

‌‌‌　　每个实体必须至少有 1 个使用 PrimaryKey 注释的字段。您还可以使用 primaryKeys 属性来定义主键。

‌‌‌　　每个实体必须要么有一个无参数构造函数，要么有一个参数匹配字段（基于类型和名称）的构造函数。构造函数不必接收所有字段作为参数，但如果字段未传递给构造函数，则它应该是公共的或具有公共设置器。如果匹配的构造函数可用，Room 将始终使用它。如果您不希望它使用构造函数，则可以使用 `Ignore` 对其进行注释。

‌‌‌　　当一个类被标记为 `Entity` 时，它的所有字段都会被持久化。如果您想排除其中的某些字段，可以使用 `Ignore` 标记它们。

‌‌‌　　如果一个字段是 `transient`，它会被自动忽略，**除非**它用 `ColumnInfo`、`Embedded` 或 `Relation` 注解。

|属性|类型|描述|
| :-----------------: | :--: | :------------------------------------------------------------: |
|tableName|`String`|SQLite 数据库中的表名|
|indices|`Array<Index>`|表上的索引列表|
|inheritSuperIndices|`Boolean`|如果设置为 `true`，则在此类的父类中定义的任何索引都将被转移到当前的 `Entity`|
|primaryKeys|`Array<String>`|主键列名称列表|
|foreignKeys|`Array<ForeignKey>`|此实体上的外键约束列表|
|ignoredColumns|`Array<String>`|Room 应忽略的列名列表|

### PrimaryKey(注解主键)

‌‌‌　　将实体中的字段标记为主键。

‌‌‌　　如果你想定义一个复合主键，你应该使用 `Entity.primaryKeys` 方法。

‌‌‌　　每个实体都必须声明一个主键，除非它的一个超类声明了一个主键。如果 Entity 及其超类都定义了 `PrimaryKey`，则子项的 `PrimaryKey` 定义将覆盖父项的 `PrimaryKey`。

‌‌‌　　如果在 `Embedded` 字段上使用 `PrimaryKey` 注释，则从该嵌入字段继承的所有列都将成为复合主键（包括其大子字段）。

|属性|类型|描述|
| :----------: | :--: | :-----------------------------------: |
|autoGenerate|`Boolean`|设置为 true 让 SQLite 自动生成唯一 ID|

### ColumnInfo(注解列)

‌‌‌　　允许对与此字段关联的列进行特定自定义。例如，您可以为字段指定列名或更改列的类型关联。

|属性|类型|描述|
| :----------: | :--: | :----------------------------------: |
|name|`String`|数据库中列的名称|
|index|`Boolean`|索引字段|
|defaultValue|`String`|此列的默认值|
|collate|`Int`|列的排序规则，将在构建数据库时使用|
|typeAffinity|`Int`|列的类型亲和性，将在构建数据库时使用|

‌‌‌　　**name 可选值：**

|常数|描述|
| :----------------: | :------------------: |
|INHERIT_FIELD_NAME|字段名作为列名的常量|

‌‌‌　　**defaultValue 可选值：**

|常数|描述|
| :---------------: | :-----------------------------------: |
|VALUE_UNSPECIFIED|defaultValue 的常量，使列没有默认值。|

‌‌‌　　**collate 可选值：**

|常数|描述|
| :---------: | :----------------------------------------------: |
|BINARY|区分大小写匹配的排序规则|
|LOCALIZED|使用系统当前语言环境的排序规则|
|NOCASE|不区分大小写匹配的排序规则|
|RTRIM|区分大小写匹配的排序规则序列，但忽略尾随空格字符|
|UNICODE|使用 Unicode 排序算法的排序序列|
|UNSPECIFIED|未指定整理顺序|

‌‌‌　　**typeAffinity 可选值：**

|常数|描述|
| :-------: | :------------------: |
|BLOB|二进制数据|
|INTEGER|整数或布尔值|
|REAL|浮点数或双精度浮点数|
|TEXT|字符串|
|UNDEFINED|未定义的类型|

### ForeignKey(注解外键)

‌‌‌　　在另一个 Entity 上声明一个外键。

‌‌‌　　外键允许您指定跨实体的约束，这样 SQLite 将确保在您修改数据库时关系有效。

‌‌‌　　当指定外键约束时，SQLite 要求引用的列是父表中唯一索引或该表主键的一部分。您必须在包含引用列的父实体中创建一个唯一索引（Room 将在编译时验证这一点，如果缺失则打印错误）。

‌‌‌　　还建议在子表上创建索引，以避免在修改父表时进行全表扫描。如果子表上缺少合适的索引，Room 将打印 RoomWarnings.MISSING_INDEX_ON_FOREIGN_KEY_CHILD 警告。

‌‌‌　　外键约束可以推迟到事务完成。如果您在单个事务中对数据库进行批量插入，这很有用。默认情况下，外键约束是即时的，但您可以通过将 deferred 设置为来 更改此值 true。您还可以使用 defer_foreign_keys 根据您的交易推迟它们。

|属性|类型|描述|
| :-----------: | :--: | :--------------------------------: |
|childColumns|`Array<String>`|当前 Entity 中的列名列表|
|entity|`KClass<*>`|要引用的父实体|
|parentColumns|`Array<String>`|父实体中的列名列表|
|deferred|`Boolean`|外键约束可以推迟到事务完成|
|onUpdate|`Int`|在数据库中更新父实体时要执行的操作|
|onDelete|`Int`|从数据库中删除父实体时要执行的操作|

‌‌‌　　**onUpdate/onDelete 可选值：**

|常数|描述|
| :-----------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|CASCADE|将父键上的删除或更新操作传播到每个从属子键。对于 onDelete 操作，<br />这意味着与已删除父行关联的子实体中的每一行也将被删除。对于 onUpdate 操作，这意味着存储在每个从属子键中的值被修改以匹配新的父键值。<br />|
|NO_ACTION|当父键被修改或从数据库中删除时，不采取任何特殊操作。|
|RESTRICT|当存在一个或多个映射到它的子键时，禁止应用程序删除（对于 onDelete）或修改（对于 onUpdate ）父键。|
|SET_DEFAULT|当父键被删除（对于 onDelete）或修改（对于 onUpdate）时，子表中映射到父键的所有行的子键列都设置为默认值|
|SET_NULL|当父键被删除（对于 onDelete）或修改（对于 onUpdate）时，子表中映射到父键的所有行的子键列都设置为 NULL 值|

### Ignore(注解 Room 忽略字段)

‌‌‌　　Room 忽略当前字段，不在数据库表中生成字段。

### Embedded(注解嵌套字段)

‌‌‌　　如果子对象和所有者对象的字段名称冲突，可以为子对象的字段指定前缀。请注意，前缀始终应用于子字段，即使它们具有带有特定 name.

‌‌‌　　如果嵌入字段的子字段具有 PrimaryKey 注释，则它们不会被视为所有者实体中的主键。

|属性|类型|描述|
| :----: | :--: | :------------------------------------: |
|prefix|`String`|指定一个前缀来添加嵌入字段中字段的列名|

## Dao（Database Access Objects 数据库访问对象）

‌‌‌　　一个 Entity 代表一张表，而每一张表都需要一个 Dao 对象，用于对表进行增删改查。

### Dao(注解数据库访问对象)

‌‌‌　　将类标记为数据访问对象。

‌‌‌　　数据访问对象是您定义数据库交互的主要类。它们可以包括多种查询方法。

‌‌‌　　用 标记的类 @Dao 应该是接口或抽象类。在编译时，Room 将在被 Database 引用时生成此类的实现 。

‌‌‌　　抽象 @Dao 类可以选择有一个将数据库 作为其唯一参数的构造函数。

### Insert(注解插入方法)

‌‌‌　　该方法的实现会将其参数插入数据库。

|属性|类型|描述|
| :--------: | :--: | :----------------: |
|entity|`KClass<*>`|插入方法的目标实体|
|onConflict|`Int`|如果发生冲突怎么办|

‌‌‌　　**onConflict 可选值：**

|常数|描述|
| :-------------------------: | :----------------------------: |
|OnConflictStrategy.ABORT|中止事务|
|OnConflictStrategy.FAIL|使事务失败，已弃用：改用 ABORT|
|OnConflictStrategy.IGNORE|忽略冲突|
|OnConflictStrategy.REPLACE|替换旧数据并继续事务|
|OnConflictStrategy.ROLLBACK|回滚事务，已弃用：改用 ABORT|

### Update(注解更新方法)

‌‌‌　　如果参数已经存在（通过主键检查），该方法的实现将更新其在数据库中的参数。如果它们不存在，则此选项不会更改数据库。

‌‌‌　　Update 方法的所有参数必须是使用 Entity 注释的类 或它的集合/数组。

|属性|类型|描述|
| :--------: | :--: | :----------------: |
|entity|`KClass<*>`|插入方法的目标实体|
|onConflict|`Int`|如果发生冲突怎么办|

‌‌‌　　**onConflict 可选值：** 参见 `Insert`

### Delete(注解删除方法)

‌‌‌　　该方法的实现将从数据库中删除其参数。

‌‌‌　　Delete 方法的所有参数必须是使用 Entity 注释的类 或它的集合/数组。

|属性|类型|描述|
| :----: | :--: | :----------------: |
|entity|`KClass<*>`|删除方法的目标实体|

### Query(注解查询方法)

‌‌‌　　注释的值包括调用此方法时将运行的查询。此查询在编译时由 Room 验证，以确保它可以针对数据库进行良好编译。

‌‌‌　　该方法的参数将绑定到 SQL 语句中的绑定参数。有关 SQLite 中绑定参数的详细信息，请参阅 SQLite 的绑定文档。

‌‌‌　　Room 仅支持命名绑定参数:name，以避免方法参数和查询绑定参数之间的任何混淆。

‌‌‌　　Room 会自动将方法的参数绑定到绑定参数中。这是通过将参数的名称与绑定参数的名称相匹配来完成的。

|属性|类型|描述|
| :---: | :--: | :------------------: |
|value|`String`|要运行的 SQLite 查询|

‌‌‌　　方法中支持 4 种类型的语句 `Query`：SELECT、INSERT、UPDATE 和 DELETE。

‌‌‌　　对于 SELECT 查询，Room 会根据方法的返回类型推断结果内容，并生成自动将查询结果转换为方法的返回类型的代码。对于单个结果查询，返回类型可以是任何数据对象（也称为 POJO）。对于返回多个值的查询，您可以使用 java.util.List 或 Array. 除此之外，任何查询都可能返回 android.database.Cursor 或者任何查询结果都可以包装在 androidx.lifecycle.LiveData 中。

‌‌‌　　INSERT 查询可以返回 void 或 Long。如果是 a Long，则该值是此查询插入的行的 SQLite rowid。请注意，插入多行的查询不能返回一个以上的 rowid，因此如果返回，请避免使用此类语句 Long。

‌‌‌　　UPDATE 或 DELETE 查询可以返回 void 或 Int。如果是 Int，则值为受此查询影响的行数。

‌‌‌　　如果您使用的是 Kotlin，您还可以 `Flow<T>` 从查询方法返回。这将创建一个 `Flow<T>` 对象，该对象发出查询结果并在每次查询表中的数据发生更改时重新调度查询。

‌‌‌　　请注意，查询返回类型为的表 `Flow<T>` 总是返回结果集中的第一行，而不是按顺序发出所有行。要观察表中多行的变化，请改用返回类型 `Flow<List<T>>`。

‌‌‌　　选择返回类型时请记住可空性，因为它会影响查询方法处理空表的方式：

‌‌‌　　* 返回类型为 `Flow<T>` 时，查询空表会抛出空指针异常。
‌‌‌　　* 返回类型为 `Flow<T?>` 时，查询空表会发出空值。
‌‌‌　　* 当返回类型为 `Flow<List<T>>` 时，查询空表会发出一个空列表。

### RawQuery(注解原始查询)

### Transaction(注解事务方法)

‌‌‌　　当用于抽象 Dao 类的非抽象方法时，该方法的派生实现将执行数据库事务中的超级方法。保留所有参数和返回类型。除非在方法体中抛出异常，否则事务将被标记为成功。

```kotlin
‌‌‌　　@Dao
‌‌‌　　abstract class SongDao {
    @Insert
    abstract fun insert(song: Song)
    @Delete
    abstract fun delete(song: Song)
    @Transaction
    fun insertAndDeleteInTransaction(newSong: Song, oldSong: Song) {
        // Anything inside this method runs in a single transaction.
        insert(newSong)
        delete(oldSong)
    }
‌‌‌　　}
```

‌‌‌　　当用于具有语句的 Query 方法时，为 QuerySELECT 生成的代码将在事务中运行。在 2 种主要情况下，您可能希望这样做：

‌‌‌　　* 如果查询的结果相当大，最好在事务中运行它以获得一致的结果。否则，如果查询结果不适合单个 android.database.CursorWindow，则查询结果可能会由于光标窗口交换之间的数据库更改而损坏。
‌‌‌　　* 如果查询结果是带有 {@link Relation} 字段的 POJO，则分别查询这些字段。要在这些查询之间获得一致的结果，您还希望在单个事务中运行它们。

## Database(数据库)

### Database(注解数据库)

‌‌‌　　将类标记为 RoomDatabase。该类应该是一个抽象类并扩展 `androidx.room.RoomDatabase`。

‌‌‌　　数据库会通过每个 @Dao 的抽象“getter”方法公开 DAO

|属性|类型|描述|
| :------------: | :--: | :--------------------------------------------------------------------------------------: |
|entities|`Array<KClass<*>>`|数据库中包含的实体列表|
|views|`Array<KClass<*>>`|数据库中包含的数据库视图列表|
|exportSchema|`Boolean`|您可以设置注释处理器参数 ( room.schemaLocation) 来告诉 Room 将数据库模式导出到文件夹中。|
|version|`Int`|数据库版本|
|autoMigrations|`Array<AutoMigration>`|可以在此数据库上执行的自动迁移列表|

### DatabaseView(注解数据库视图)

‌‌‌　　将类标记为 SQLite 视图。

‌‌‌　　注释的值是创建视图时使用的 `SELECT` 查询。

‌‌‌　　当在 Dao 中使用该类时，它的行为类似于普通 POJO 。您可以从 类似于 `Entity` 的 `DatabaseView` 中选择，但您不能 `INSERT`、`DELETE` 或 `UPDATE` 到 `DatabaseView` 中。

‌‌‌　　与 Entity 类似，您可以使用 `ColumnInfo` 和 `Embedded inside` 来自定义数据类。

```kotlin
‌‌‌　　@DatabaseView(
  "SELECT id, name, release_year FROM Song " +
  "WHERE release_year >= 1990 AND release_year <= 1999"
‌‌‌　　)
‌‌‌　　data class SongFrom90s (
  val id: Long,
  val name: String,
  @ColumnInfo(name = "release_year")
  val releaseYear: Int
‌‌‌　　)
```

‌‌‌　　视图必须通过 `Database.views` 注册到 `RoomDatabase` 。

|属性|类型|描述|
| :------: | :--: | :-----------------------: |
|value|`String`|SELECT 查询|
|viewName|`String`|SQLite 数据库中的视图名称|

## Relation(注解实体关系)

‌‌‌　　可以在 POJO 中使用以自动获取关系实体的便捷注释。当 POJO 从查询返回时，它的所有关系也由 Room 获取。

‌‌‌　　对于一对多或多对多关系，使用注释的字段类型 Relation 必须是 java.util.List 或 java.util.Set。

‌‌‌　　默认情况下，Entity 类型是从返回类型推断出来的。如果你想返回一个不同的对象，你可以在注解中指定实体属性。

‌‌‌　　如果关系是由关联表（也称为联结表）定义的，那么您可以使用 associateBy 来指定它。这对于获取多对多关系很有用。

‌‌‌　　注意 @Relation 注解只能在 POJO 类中使用，Entity 类不能有关系。这是避免实体 设置中常见陷阱的设计决策。您可以在主 Room 文档中阅读更多相关信息。加载数据时，您可以通过创建扩展 Entity 的 POJO 类来解决此限制。

|属性|类型|描述|
| :----------: | :--: | :--------------------------------------------------: |
|entity|`KClass<*>`|要从中获取项目的实体或视图|
|parentColumn|`String`|父 POJO 中的引用列|
|entityColumn|`String`|实体中要匹配的列|
|associateBy|`Junction`|获取相关实体时用作关联表（也称为联结表）的实体或视图|
|projection|`Array<String>`|如果应从实体中获取子列，您可以使用此字段指定它们|
‌‌‌　　