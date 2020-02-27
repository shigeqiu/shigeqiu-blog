


# SimplePrincipalCollection
该对象的主要属性如下：

```java
public class SimplePrincipalCollection implements MutablePrincipalCollection {

    private static final long serialVersionUID = -6305224034025797558L;

    //TODO - complete JavaDoc

    private Map<String, Set> realmPrincipals;

    private transient String cachedToString; //cached toString() result, as this can be printed many times in logging
}
```
其中`realmPrincipals`的key为Realm值，value为Set集合。

#