继承`BaseMapper<T>` 可以获得通用的CRUD方法：
```java
public interface UserMapper extends BaseMapper<User> {  
}
```
# 插入
---
- `int insert(T entity)`：插入一条记录。
# 查询
---
- `T selectById(Serializable id)`：根据主键 ID 查询单条记录。
- `List<T> selectBatchIds(Collection<?> idList)`根据主键 ID 集合，批量查询多条记录。  
- `List<T> selectByMap(Map<String, Object> columnMap)`：根据`<列名, 条件值>`Map查询符合条件的记录。
- `T selectOne(Wrapper<T> queryWrapper)`：根据条件构造器查询一条记录。（如果查询结果有多条，会抛出异常）。
- `List<T> selectList(Wrapper<T> queryWrapper)`：根据条件构造器查询结果列表。
- `Long selectCount(Wrapper<T> queryWrapper)`：根据条件构造器查询符合条件的记录总数。
- `List<Map<String, Object>> selectMaps(Wrapper<T> queryWrapper)`：根据条件构造器查询，将结果封装为 `List<Map<String, Object>>`（每行数据为一个 Map）。
- `List<Object> selectObjs(Wrapper<T> queryWrapper)`：根据条件构造器查询，仅返回第一列的数据列表（例如只取所有的 ID 列表）。
# 更新
---
- `int updateById(T entity)`：根据主键更新一条记录，仅更新非null字段。
- `int update(T entity, Wrapper<T> updateWrapper)`：根据条件构造器更新。可以把 `entity` 传 `null`，只在 Wrapper 里指定 update set 条件。
# 删除
---
- `int deleteById(Serializable id)`：根据主键删除一条记录。
- `int deleteById(T entity)`：根据实体的主键删除一条记录。
- `int deleteBatchIds(Collection<?> idList)`：根据主键 ID 集合，批量删除记录。
- `int deleteByMap(Map<String, Object> columnMap)`：根据`<列名, 条件值>`Map删除符合条件的记录。
- `int delete(Wrapper<T> queryWrapper)`：根据条件构造器匹配删除记录。