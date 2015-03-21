This API is _Hibernate-like_ RIDC (Remote Intradoc Client) API for [Oracle Content Server (Oracle UCM)](http://www.oracle.com/technetwork/middleware/content-management/cs-088869.html). It provides two ways to get data from Oracle Content Server by using DocumentService _(RIDC query language and Database Native SQL)_ and Hibernate-like Criteria _(Database Native SQL)_.

Following are some examples to use **RIDCku** to get content from server:
### Using DocumentService ###
**Example 1.** Getting contents using RIDC query language with pagination
```
Session session = Session.getInstance();

// Example to use RIDC query language with pagination
// Format:
// public List<Document> getSearchResult(String query,
//     Map<String, Object> params, List<Order> sortOrders,
//     String resultSet, long... startIndexAndSize)
//     throws IdcClientException, IOException, ClassNotFoundException,
//     IllegalAccessException, NoSuchMethodException,
//     InvocationTargetException;
List<Document> ridcQueryDocs = session.getDocumentService().getSearchResult(
    "(dDocType <substring> `Digital` <AND> dDocTitle <substring> `Test`) <OR> " +
    "(dDocType <substring> `News` <AND> dDocTitle <substring> `Robertus Lilik Haryanto`)",
    null, null, null, 2, 10);
```


**Example 2.** Getting contents using NATIVE SQL query with pagination
```
Session session = Session.getInstance();

// Example to use NATIVE SQL query with pagination
// Format:
// public List<Document> getSearchResult(SearchQueryFormat searchQueryFormat,
//     String query, Map<String, Object> params, List<Order> sortOrders,
//     String resultSet, long... startIndexAndSize)
//     throws IdcClientException, IOException, ClassNotFoundException,
//     IllegalAccessException, NoSuchMethodException,
//     InvocationTargetException;
List<Document> nativeQueryDocs = session.getDocumentService().getSearchResult(SearchQueryFormat.NATIVE,
    "UPPER(dDocType) = 'Document' AND UPPER(dDocTitle) LIKE '%ROBERTUS LILIK HARYANTO%'", null, null, null, 0, 5);
```

### Using Hibernate-like Criteria ###

If you familiar with [Criteria in Hibernate](http://docs.jboss.org/hibernate/orm/3.6/javadocs/org/hibernate/Criteria.html), then it's easier for you to learn how to use this API.


**Example 3.** Getting contents using Hibernate-like Criteria without pagination
```
Session session = Session.getInstance();

// Example to use Hibernate-like Criteria without pagination
List<Document> criteriaDocs = session.createCriteria()
    .add(Restrictions.like("dDocTitle", "%Robertus Lilik Haryanto%"))
    .addOrder(Order.asc("dDocType"))
    .setFirstResult(0)
    .setMaxResults(100)
    .list();
```


**Example 4.** Getting contents using Hibernate-like Criteria with pagination for multiple Document Type
```
Session session = Session.getInstance();

// Example to use Hibernate-like Criteria with pagination for multiple Document Type
Criteria multipleCriteria = session.createCriteria()
    .add(
         Restrictions.notStartsWith("Revisions.dDocName", "NULL")
    )
    .add(
         Restrictions.in("dDocType", "Wiki", "DigitalMedia", "Blog", "Document")
    )
    .add(
        Restrictions.or(
            Restrictions.like("Revisions.dDocName", "%docName%"),
            Restrictions.like("dDocTitle", "%title%"),
            Restrictions.like("xComments", "%comment%"),
            Restrictions.like("xWCTags", "%tag1%")
        )
     )
     .setFirstResult(0)
     .setMaxResults(1000);

PaginatedResultList<Document> paginatedMultipleDocs = (PaginatedResultList<Document>) multipleCriteria.paginatedList();
```


**Example 5.** Getting contents using Hibernate-like Criteria with pagination for single Document Type
```
Session session = Session.getInstance();

// Example to use Hibernate-like Criteria with pagination for single Document Type
Criteria singleCriteria = session.createCriteria("Document")
    .add(
        Restrictions.or(
            Restrictions.like("dDocTitle", "%title%"),
            Restrictions.like("xComments", "%Comment by Robertus Lilik Haryanto%"),
            Restrictions.like("xWCTags", "%tag1%")
        )
     )
     .setFirstResult(0)
     .setMaxResults(500);

PaginatedResultList<Document> paginatedSingleDocs = (PaginatedResultList<Document>) singleCriteria.paginatedList();
```

Following annotations also provided by this API to help Developers in customizing this API:
  * **Meta** - Annotation for content's attribute
  * **IdcService** - Annotation for IDC service
  * **OrderParam** - Annotation for parameter Order
  * **ResultSetParam**  - Annotation for parameter IDC ResultSet name
  * **SearchQueryFormatParam** - Annotation for pamaterer query format (DEFAULT or NATIVE)

For more detail and features, coming soon.

You also can find some API documentations below:
  * **[RIDCku-0.0.1-SNAPSHOT](http://projects.secangkirkopipanas.com/ridcku/0.0.1-SNAPSHOT/apidocs/index.html)**
  * **[RIDC](http://docs.oracle.com/cd/E10316_01/ContentIntegration/ridc/Javadoc/index.html)**