---
title: FIL_PAGE_INDEX
description: FIL_PAGE_INDEX
date: 2023-10-30
weight: 10000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert color="secondary">}}
表空间中的每一个页都对应着一个页号，也就是 FIL_PAGE_OFFSET 。

这个页号由4个字节组成，也就是32个比特位，所以一个表空间最多可以拥有$2^{32}$个页，如果按照页的默认大小16KB来算，一个表空间最多支持64TB的数据。

表空间的第一个页的页号为0，之后的页号分别是1，2，3...依此类推

{{</alert>}}



## 一、页面类型

页面类型前边都有个 `FIL_PAGE` 或者 `FIL_PAGE_TYPE` 的前缀

| 页面类型                | 功能                  | 备注  |
| :---------------------- | :-------------------- | :---- |
| FIL_PAGE_INDEX          | B+树页面类型          | 17855 |
| FIL_PAGE_RTREE          | R+树页面类型          | 17854 |
| FIL_PAGE_UNDO_LOG       | Undo日志页            | 2     |
| FIL_PAGE_INODE          | 段信息节点            | 3     |
| FIL_PAGE_IBUF_FREE_LIST | Insert Buffer空闲列表 | 4     |
| FIL_PAGE_TYPE_SYS       | 系统页面              | 6     |
| FIL_PAGE_TYPE_ALLOCATED | 最新分配，还没使用    | 0     |


```c++

using page_type_t = uint16_t;

/** File page types (values of FIL_PAGE_TYPE) @{ */
/** B-tree node */
constexpr page_type_t FIL_PAGE_INDEX = 17855;

/** R-tree node */
constexpr page_type_t FIL_PAGE_RTREE = 17854;

/** Tablespace SDI Index page */
constexpr page_type_t FIL_PAGE_SDI = 17853;

/** This page type is unused. */
constexpr page_type_t FIL_PAGE_TYPE_UNUSED = 1;

/** Undo log page */
constexpr page_type_t FIL_PAGE_UNDO_LOG = 2;

/** Index node */
constexpr page_type_t FIL_PAGE_INODE = 3;

/** Insert buffer free list */
constexpr page_type_t FIL_PAGE_IBUF_FREE_LIST = 4;

/* File page types introduced in MySQL/InnoDB 5.1.7 */
/** Freshly allocated page */
constexpr page_type_t FIL_PAGE_TYPE_ALLOCATED = 0;

/** Insert buffer bitmap */
constexpr page_type_t FIL_PAGE_IBUF_BITMAP = 5;

/** System page */
constexpr page_type_t FIL_PAGE_TYPE_SYS = 6;

/** Transaction system data */
constexpr page_type_t FIL_PAGE_TYPE_TRX_SYS = 7;

/** File space header */
constexpr page_type_t FIL_PAGE_TYPE_FSP_HDR = 8;

/** Extent descriptor page */
constexpr page_type_t FIL_PAGE_TYPE_XDES = 9;

/** Uncompressed BLOB page */
constexpr page_type_t FIL_PAGE_TYPE_BLOB = 10;

/** First compressed BLOB page */
constexpr page_type_t FIL_PAGE_TYPE_ZBLOB = 11;

/** Subsequent compressed BLOB page */
constexpr page_type_t FIL_PAGE_TYPE_ZBLOB2 = 12;

/** In old tablespaces, garbage in FIL_PAGE_TYPE is replaced with
this value when flushing pages. */
constexpr page_type_t FIL_PAGE_TYPE_UNKNOWN = 13;

/** Compressed page */
constexpr page_type_t FIL_PAGE_COMPRESSED = 14;

/** Encrypted page */
constexpr page_type_t FIL_PAGE_ENCRYPTED = 15;

/** Compressed and Encrypted page */
constexpr page_type_t FIL_PAGE_COMPRESSED_AND_ENCRYPTED = 16;

/** Encrypted R-tree page */
constexpr page_type_t FIL_PAGE_ENCRYPTED_RTREE = 17;

/** Uncompressed SDI BLOB page */
constexpr page_type_t FIL_PAGE_SDI_BLOB = 18;

/** Compressed SDI BLOB page */
constexpr page_type_t FIL_PAGE_SDI_ZBLOB = 19;

/** Legacy doublewrite buffer page. */
constexpr page_type_t FIL_PAGE_TYPE_LEGACY_DBLWR = 20;

/** Rollback Segment Array page */
constexpr page_type_t FIL_PAGE_TYPE_RSEG_ARRAY = 21;

/** Index pages of uncompressed LOB */
constexpr page_type_t FIL_PAGE_TYPE_LOB_INDEX = 22;

/** Data pages of uncompressed LOB */
constexpr page_type_t FIL_PAGE_TYPE_LOB_DATA = 23;

/** The first page of an uncompressed LOB */
constexpr page_type_t FIL_PAGE_TYPE_LOB_FIRST = 24;

/** The first page of a compressed LOB */
constexpr page_type_t FIL_PAGE_TYPE_ZLOB_FIRST = 25;

/** Data pages of compressed LOB */
constexpr page_type_t FIL_PAGE_TYPE_ZLOB_DATA = 26;

/** Index pages of compressed LOB. This page contains an array of
z_index_entry_t objects.*/
constexpr page_type_t FIL_PAGE_TYPE_ZLOB_INDEX = 27;

/** Fragment pages of compressed LOB. */
constexpr page_type_t FIL_PAGE_TYPE_ZLOB_FRAG = 28;

/** Index pages of fragment pages (compressed LOB). */
constexpr page_type_t FIL_PAGE_TYPE_ZLOB_FRAG_ENTRY = 29;

/** Note the highest valid non-index page_type_t. */
constexpr page_type_t FIL_PAGE_TYPE_LAST = FIL_PAGE_TYPE_ZLOB_FRAG_ENTRY;

```

