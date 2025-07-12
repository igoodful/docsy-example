---
title: trx_undo_t
description: trx_undo_t
date: 2023-10-30
weight: 10000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

{{<alert color="danger" title="注意" >}}

- 在每台服务器上执行
- 在进行下一步之前，您必须确保错误日志中没有生成任何警告

{{</alert>}}




## trx_undo_t对象

| 成员             | 类型                         | 功能 |
| :--------------- | :--------------------------- | :--- |
| `id`             | `ulint`                      |      |
| `type`           | `ulint`                      |      |
| `state`          | `ulint`                      |      |
| `del_marks`      | `bool`                       |      |
| `trx_id`         | `trx_id_t`                   |      |
| `xid`            | `XID`                        |      |
| `flag`           | `ulint`                      |      |
| `m_gtid_storage` | `Gtid_storage`               |      |
| `dict_operation` | `bool`                       |      |
| `rseg`           | `trx_rseg_t *`               |      |
| `space`          | `space_id_t`                 |      |
| `page_size`      | `page_size_t`                |      |
| `hdr_page_no`    | `page_no_t`                  |      |
| `hdr_offset`     | `ulint`                      |      |
| `last_page_no`   | `page_no_t`                  |      |
| `size`           | `ulint`                      |      |
| `empty`          | `ulint`                      |      |
| `top_page_no`    | `page_no_t`                  |      |
| `top_offset`     | `ulint`                      |      |
| `top_undo_no`    | `undo_no_t`                  |      |
| `guess_block`    | `buf_block_t *`              |      |
| `undo_list`      | `UT_LIST_NODE_T(trx_undo_t)` |      |

```c++

struct trx_undo_t {
        /** Undo log may could be allocated to store transaction GTIDs. */
        enum class Gtid_storage {
                /* No storage is allocated for GTID. */
                NONE,
                /* Storage is allocated for commit GTID. */
                COMMIT,
                /* Storage is allocated for both prepare and commit GTID. For external
                XA transaction, we have GTID fr both prepare and commit. */
                PREPARE_AND_COMMIT
        };

        /** Check if space for GTID is allocated in undo.
        @param[in]    is_prepare      if XA prepare GTID
        @return true iff space for GTID is allocated. */
        bool gtid_allocated(bool is_prepare) const;

        /** Get offset and flag for GTID stored in undo.
        @param[in]    is_prepare      if XA prepare GTID
        @return GTID flag and offset in a tuple. */
        std::tuple<int, size_t> gtid_get_details(bool is_prepare) const;

        /* Set undo segment to prepared state and set XID
        @param[in]     in_xid transaction XID. */
        inline void set_prepared(const XID *in_xid);

        /* Set undo segment to prepared in TC state and set XID */
        inline void set_prepared_in_tc();

        /* Checks whether or not this undo log segment is in prepared state, meaning,
        the `state` member variable is either `TRX_UNDO_PREPARED_80028`.
        `TRX_UNDO_PREPARED` or `TRX_UNDO_PREPARED_IN_TC`.
        @return true is the undo log segment is in prepared state, false otherwise.*/
        inline bool is_prepared() const;

        /*-----------------------------*/
        ulint id;        /*!< undo log slot number within the
                         rollback segment */
        ulint type;      /*!< TRX_UNDO_INSERT or
                         TRX_UNDO_UPDATE */
        ulint state;     /*!< state of the corresponding undo log
                         segment */
        bool del_marks;  /*!< relevant only in an update undo
                          log: this is true if the transaction may
                          have delete marked records, because of
                          a delete of a row or an update of an
                          indexed field; purge is then
                          necessary; also true if the transaction
                          has updated an externally stored
                          field */
        trx_id_t trx_id; /*!< id of the trx assigned to the undo
                         log */
        XID xid;         /*!< X/Open XA transaction
                         identification */
        ulint flag;      /*!< flag for current transaction XID and GTID.
                         Persisted in TRX_UNDO_FLAGS flag of undo header. */

        /** Storage space allocated for GTIDs. */
        Gtid_storage m_gtid_storage{Gtid_storage::NONE};

        bool dict_operation; /*!< true if a dict operation trx */
        trx_rseg_t *rseg;    /*!< rseg where the undo log belongs */
        /*-----------------------------*/
        space_id_t space; /*!< space id where the undo log
                          placed */
        page_size_t page_size;
        page_no_t hdr_page_no;  /*!< page number of the header page in
                                the undo log */
        ulint hdr_offset;       /*!< header offset of the undo log on
                                the page */
        page_no_t last_page_no; /*!< page number of the last page in the
                                undo log; this may differ from
                                top_page_no during a rollback */
        ulint size;             /*!< current size in pages */
        /*-----------------------------*/
        ulint empty;              /*!< true if the stack of undo log
                                  records is currently empty */
        page_no_t top_page_no;    /*!< page number where the latest undo
                                  log record was catenated; during
                                  rollback the page from which the latest
                                  undo record was chosen */
        ulint top_offset;         /*!< offset of the latest undo record,
                                  i.e., the topmost element in the undo
                                  log if we think of it as a stack */
        undo_no_t top_undo_no;    /*!< undo number of the latest record */
        buf_block_t *guess_block; /*!< guess for the buffer block where
                                  the top page might reside */
        /*-----------------------------*/
        UT_LIST_NODE_T(trx_undo_t) undo_list;
        /*!< undo log objects in the rollback
        segment are chained into lists */
};

```




```c++
typedef struct xid_t XID;

typedef struct xid_t {
private:
        /**
          -1 means that the XID is null
        */
        long formatID;

        /**
          value from 1 through 64
        */
        long gtrid_length;

        /**
          value from 1 through 64
        */
        long bqual_length;

        /**
          distributed trx identifier. not \0-terminated.
        */
        char data[XIDDATASIZE];

public:
        xid_t() : formatID(-1), gtrid_length(0), bqual_length(0) {
                memset(data, 0, XIDDATASIZE);
        }

        long get_format_id() const {
                return formatID;
        }

        void set_format_id(long v) {
                DBUG_TRACE;
                DBUG_PRINT("debug", ("SETTING XID_STATE formatID: %ld", v));
                formatID = v;
                return;
        }

        long get_gtrid_length() const {
                return gtrid_length;
        }

        void set_gtrid_length(long v) {
                gtrid_length = v;
        }

        long get_bqual_length() const {
                return bqual_length;
        }

        void set_bqual_length(long v) {
                bqual_length = v;
        }

        const char *get_data() const {
                return data;
        }

        void set_data(const void *v, long l) {
                assert(l <= XIDDATASIZE);
                memcpy(data, v, l);
        }

        void reset() {
                formatID     = -1;
                gtrid_length = 0;
                bqual_length = 0;
                memset(data, 0, XIDDATASIZE);
        }

        void set(long f, const char *g, long gl, const char *b, long bl) {
                DBUG_TRACE;
                DBUG_PRINT("debug", ("SETTING XID_STATE formatID: %ld", f));
                formatID = f;
                memcpy(data, g, gtrid_length = gl);
                bqual_length = bl;
                if (bl > 0)
                        memcpy(data + gl, b, bl);
                return;
        }

        my_xid get_my_xid() const;

        uchar *key() {
                return reinterpret_cast<uchar *>(&gtrid_length);
        }

        const uchar *key() const {
                return reinterpret_cast<const uchar *>(&gtrid_length);
        }

        uint key_length() const {
                return sizeof(gtrid_length) + sizeof(bqual_length) + gtrid_length + bqual_length;
        }

        /*
          The size of the string containing serialized Xid representation
          is computed as a sum of
            eight as the number of formatting symbols (X'',X'',)
            plus 2 x XIDDATASIZE (2 due to hex format),
            plus space for decimal digits of XID::formatID,
            plus one for 0x0.
         */
        static const uint ser_buf_size = 8 + 2 * XIDDATASIZE + 4 * sizeof(long) + 1;

        /**
           The method fills XID in a buffer in format of GTRID,BQUAL,FORMATID
           where GTRID, BQUAL are represented as hex strings.

           @param  buf  a pointer to buffer
           @return the value of the first argument
        */

        char *serialize(char *buf) const {
                return serialize_xid(buf, formatID, gtrid_length, bqual_length, data);
        }

#ifndef NDEBUG
        /**
           Get printable XID value.

           @param buf  pointer to the buffer where printable XID value has to be
           stored

           @return  pointer to the buffer passed in the first argument
        */
        char *xid_to_str(char *buf) const;
#endif
        /**
          Check if equal to another xid.

          @param[in]  xid   the id of another X/Open XA transaction

          @return true iff formats, gtrid_length, bqual_length and the content of
                  gtrid_length+bqual_length bytes is exactly the same
        */
        bool eq(const xid_t *xid) const {
                return xid->formatID == formatID && xid->gtrid_length == gtrid_length && xid->bqual_length == bqual_length &&
                       !memcmp(xid->data, data, gtrid_length + bqual_length);
        }

        bool is_null() const {
                return formatID == -1;
        }

        /**
          Instantiates this object with the contents of the parameter of type
          `XA_prepare_event::MY_XID`.

          The `XA_prepare_event::MY_XID` is a mirror class of `xid_t` so the
          instantiation is a direct instantiation relation of each class member
          variables.

          @param rhs The `XA_prepare_event::MY_XID` to instantiate from

          @return This object reference.
         */
        xid_t &operator=(binary_log::XA_prepare_event::MY_XID const &rhs);
        /**
          Compares for equality two instances of `xid_t`.

          @param rhs The instance to compare this object with.

          @return true if both instance are equal, false otherwise.
         */
        bool operator==(struct xid_t const &rhs) const;
        /**
          Compares for inequality two instances of `xid_t`.

          @param rhs The instance to compare this object with.

          @return true if both instance are different, false otherwise.
         */
        bool operator!=(struct xid_t const &rhs) const;
        /**
          Compares for lower-than-inequality two instances of `xid_t`.

          The lesser-than relation between two given XIDs, x1 and x2, is as
          follows:

          x1 < x2 if any of the following is true:
          - x1[FORMAT_ID] < x2[FORMAT_ID]
          - x1[strlen(GTRID)] < x2[strlen(GTRID)]
          - x1[strlen(BQUAL)] < x2[strlen(BQUAL)]
          - std::strncmp(x1[DATA], x2[DATA]) < 0

          @param rhs The instance to compare this object with.

          @return true if this instance is lesser than the parameter, false
                  otherwise.
         */
        bool operator<(struct xid_t const &rhs) const;
        /**
          Writes the parameter's `in` string representation to the `out` stream
          parameter object.

          @param out The stream to write the XID representation to
          @param in  The XID for which the string representation should be written

          @return The reference for the stream passed on as parameter.
         */
        friend std::ostream &operator<<(std::ostream &out, struct xid_t const &in);

private:
        void set(const xid_t *xid) {
                memcpy(this, xid, sizeof(xid->formatID) + xid->key_length());
        }

        void set(my_xid xid);

        void null() {
                formatID = -1;
        }

        friend class XID_STATE;
} XID;

```








