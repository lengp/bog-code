---
title: spring boot jpa 复杂条件查询
categories: 技术分享    
---
#  1 
```
 @Query(value = "select new com.xxx.xx.xx.XXXVo (id,name1,t2.name2) " +
      "From table1 t1 left join table2 t2  on t1.xId = t2.id  " +
      " where t1.id = :corpId and t2.name = :name  ")
  Page<XXXVo> searchMyCustomer(@Param("id") String id, @Param("name") String name, Pageable pageable);

```
# 2
```
  @Query(value = "select name,`id` id,`birthday` birthday,`cooperateuids` cooperateUIds,`corp_full_name` corpFullName ,`corp_name` corpName ,`corp_id` corpId from customer " +
      "where IF (?2 != '', name=?2, 1=1) " +
      " and IF (?1 != '', corp_id=?1, 1=1) ",
      countQuery = "select count(*) from customer " +
          "where IF (?2 != '', name=?2, 1=1) " +
          " and IF (?1 != '', corp_id=?1, 1=1) ", nativeQuery = true)
  Page<Map<String, Object>> searchMyCustomerNative2(String corpId, String name, Pageable pageable);
```
  # 3
  ```
   String querySql = "select `id` id,`birthday` birthday,`cooperateuids` cooperateUIds,`corp_full_name` corpFullName ,`corp_name` corpName ,`corp_id` corpId from customer where name ='测试客户1' and corp_id='1'";
//    //查询数据条数
    String countSql = "SELECT COUNT(*)  FROM ( " + querySql + " ) getcount";
    Query countQuery = entityManager.createNativeQuery(countSql);
    Object obj = countQuery.getSingleResult();
    //总条数
    int totalCount = Integer.parseInt(obj.toString());

    Query query = entityManager.createNativeQuery(querySql);
    query.setFirstResult((int) pageable.getOffset());
    query.setMaxResults(pageable.getPageSize());
    query.unwrap(NativeQueryImpl.class).setResultTransformer(Transformers.ALIAS_TO_ENTITY_MAP);
    Object obj1 = query.getResultList();
```
# 4

```
//    Specification<Customer> spec = (Specification<Customer>) (root, query, cb) -> {
//      List<Predicate> list = new ArrayList<>();
//      if(StringUtils.isNotEmpty(uId)) {
//        Predicate pre = cb.equal(root.get("lastFollowUId"), uId);
//        list.add(pre);
//      }
//      Join<Customer, User> join = root.join("lastFollowUId", JoinType.LEFT);
//
//      Predicate[] arr = new Predicate[list.size()];
//      return cb.and(list.toArray(arr));
//    };
//    return customerRepo.findAll(spec);
```
