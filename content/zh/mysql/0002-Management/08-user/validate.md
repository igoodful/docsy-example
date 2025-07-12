---
title: validate
description: validate
date: 2023-10-25
weight: 20000
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{<note>}}
<!---->


{{</note>}}



## validate_password
```sql
install plugin validate_password soname 'validate_password.so';
uninstall plugin validate_password;


```

## component_validate_password
```sql
install component 'file://component_validate_password';
uninstall component 'file://component_validate_password';

```


##


```sql
show variables like 'validate%';

```























