---
layout: post
category : lucence
date: 2013/10/11 9:21:52 
title: Lucence对索引进行增删改查
tags : [lucence]
---
{% include JB/setup %}

###Filed.index(索引选项)
- Index.ANALYZED：进行分词和索引，适用用于标题和内容
- Index.NOT_ANALYZED:不进行分词，进行索引，如果身份证号码，姓名，id等，适用于精确搜索
- Index>ANALYZED_NOT_NORNS：进行分词但是不存储norms信息。这个norms中包括了创建索引的时间和权值信息
- Index.NOT_NOT_ANALYZED_NOT_NORMS:即不进行分词也不存储norms信息 Index.NO:不进行索引




<pre class="brush: java;">
public class IndexUtil {
    private String[] ids = { "1", "2", "3", "4", "5", "6" };
    private String[] emails = { "aa@itat.com", "bb@itat.org", "cc@cc.com",
            "dd@sina.org", "ee@zt.com", "ff@itat.org" };
    private String[] contents = { "welcome to vistite the spaces", "hello boy",
            "my name is cc", "I like football",
            "I like football and i like basketball too",
            "I like movie and swim" };
    private Date[] dates = null;
    private int[] attachs = { 2, 3, 1, 4, 5, 6 };
    private String names[] = { "zhangsan", "lisi", "john", "jetty", "mike",
            "jake" };
    private Directory directory = null;
    private Analyzer analyzer = null;
    private IndexWriterConfig config = null;
    public IndexUtil() {
        try {
            setDates();
            analyzer = new StandardAnalyzer(Version.LUCENE_35);
            directory = FSDirectory.open(new File("F:/lucene/index02"));
            config = new IndexWriterConfig(Version.LUCENE_35, analyzer);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    private void setDates() {
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
        dates = new Date[ids.length];
        try {
            dates[0] = sdf.parse("2010-02-19");
            dates[1] = sdf.parse("2010-02-20");
            dates[2] = sdf.parse("2010-02-11");
            dates[3] = sdf.parse("2010-02-12");
            dates[4] = sdf.parse("2010-02-13");
            dates[5] = sdf.parse("2010-02-15");
        } catch (ParseException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
    /**
     * Filed.index(索引选项)
     * <p>
     * Index.ANALYZED：进行分词和索引，适用用于标题和内容
     * Index.NOT_ANALYZED:不进行分词，进行索引，如果身份证号码，姓名，id等，适用于精确搜索
     * Index>ANALYZED_NOT_NORNS：进行分词但是不存储norms信息。这个norms中包括了创建索引的时间和权值信息
     * Index.NOT_NOT_ANALYZED_NOT_NORMS:即不进行分词也不存储norms信息 Index.NO:不进行索引
     * */
    public void index() {
        IndexWriter writer = null;
        try {
            writer = new IndexWriter(directory, new IndexWriterConfig(
                    Version.LUCENE_35, analyzer));
            Document doc = null;
            for (int i = 0; i < ids.length; i++) {
                doc = new Document();
                doc.add(new Field("id", ids[i], Field.Store.YES,
                        Index.NOT_ANALYZED_NO_NORMS));
                doc.add(new Field("email", emails[i], Field.Store.YES,
                        Field.Index.NOT_ANALYZED));
                doc.add(new Field("content", contents[i], Field.Store.NO,
                        Field.Index.ANALYZED));
                doc.add(new Field("name", names[i], Field.Store.YES,
                        Field.Index.NOT_ANALYZED_NO_NORMS));
                // 存储数字
                doc.add(new NumericField("attachs", Field.Store.YES, true)
                        .setIntValue(attachs[i]));
                // 给日期加索引
                doc.add(new NumericField("date", Field.Store.YES, true)
                        .setLongValue(dates[i].getTime()));
                writer.addDocument(doc);
            }
        } catch (CorruptIndexException e) {
            e.printStackTrace();
        } catch (LockObtainFailedException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (writer != null) {
                try {
                    writer.close();
                } catch (CorruptIndexException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    /**
     * 查询
     * */
    public void query() {
        try {
            /**
             * numberDocs6 maxDocs6
             * */
            // 通过reader可以有效的获取文档的数量
            IndexReader reader = IndexReader.open(directory);
            System.out.println("numberDocs:" + reader.numDocs());
            System.out.println("maxDocs:" + reader.maxDoc());
            System.out.println("deleteDocs:" + reader.numDeletedDocs());
            reader.close();
        } catch (CorruptIndexException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public void search() {
        try {
            IndexReader reader = IndexReader.open(directory);
            IndexSearcher searcher = new IndexSearcher(reader);
            TermQuery query = new TermQuery(new Term("content", "like"));
            TopDocs topDocs = searcher.search(query, 10);
            for (ScoreDoc scoreDoc : topDocs.scoreDocs) {
                Document document = searcher.doc(scoreDoc.doc);
                System.out.println("(" + scoreDoc.doc + "):"
                        + document.get("name") + "[" + document.get("email")
                        + "]-->" + document.get("attachs") + ","
                        + document.get("date") +","+ document.get("id"));
            }
        } catch (CorruptIndexException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
    /**
     * 删除索引
     * */
    public void delete() {
        IndexWriter writer = null;
        try {
            writer = new IndexWriter(directory, config);
            // 参数是一个选项，可以是一个Query(查询的结果)，也可以是一个Term(精确查找的值)
            // 此时删除的文档并不会完成删除，而是存储在一个del文件里面。可以恢复
            writer.deleteDocuments(new Term("id", "1"));
        } catch (CorruptIndexException e) {
            e.printStackTrace();
        } catch (LockObtainFailedException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (writer != null) {
                try {
                    writer.close();
                } catch (CorruptIndexException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    /**
     * 从回收站恢复
     * */
    public void undelete() {
        // 使用IndexReader进行恢复
        try {
            IndexReader reader = IndexReader.open(directory, false);
            // 恢复时，必须把IndexReader的只读设置为false
            reader.undeleteAll();
            reader.close();
        } catch (CorruptIndexException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    /**
     * 从回收站删除
     * */
    public void foreMeerge() {
        IndexWriter writer = null;
        try {
            writer = new IndexWriter(directory, config);
            writer.forceMergeDeletes();
        } catch (CorruptIndexException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (writer != null) {
                try {
                    writer.close();
                } catch (CorruptIndexException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    public void merge() {
        IndexWriter writer = null;
        try {
            writer = new IndexWriter(directory, config);
            // 将索引合并为2段，这两段中的被删除的数据会被清空
            // 特别注意，此处在3.5后不建议使用，因为会消耗大量的开销，lucence会根据情况自动处理
            writer.forceMerge(2);
        } catch (CorruptIndexException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (writer != null) {
                try {
                    writer.close();
                } catch (CorruptIndexException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    /**
     * 更新索引
     * */
    public void update() {
        IndexWriter writer = null;
        try {
            writer = new IndexWriter(directory, config);
            /**
             * Lucene并没有提供更新，这里的更新其实是 先删除之后再添加
             * */
            Document doc = new Document();
            doc.add(new Field("id", "11", Field.Store.YES,
                    Index.NOT_ANALYZED_NO_NORMS));
            doc.add(new Field("email", emails[0], Field.Store.YES,
                    Field.Index.NOT_ANALYZED));
            doc.add(new Field("content", contents[0], Field.Store.NO,
                    Field.Index.ANALYZED));
            doc.add(new Field("name", names[0], Field.Store.YES,
                    Field.Index.NOT_ANALYZED_NO_NORMS));
            writer.updateDocument(new Term("id", "1"), doc);
            writer.close();
        } catch (CorruptIndexException e) {
            e.printStackTrace();
        } catch (LockObtainFailedException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

</pre>