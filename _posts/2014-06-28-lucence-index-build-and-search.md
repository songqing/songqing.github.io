---
layout: post
category : lucence
date: 2014/06/28 1:21:52 
title: Lucence索引的建立和搜索
tags : [lucence]
---


###建立索引的步骤
1. 创建Directory
2. 创建IndexWriter
3. 创建Document对象（表）
4. 为Document添加Field(表的字段)
5. 通过IndexWriter添加文档到索引中


{% highlight java %}

     /**
     * 建立索引
     * 
     * @throws ParseException
     * @throws IOException
     */
    public void index() {
        IndexWriter writer = null;
        try {
            // 1:创建Directory
            // Directory directory = new RANDirecotry();//索引建立在内存中
            // 创建在硬盘上。根据当前环境打开一个合理的索引位置。
            Directory directory = FSDirectory
                    .open(new File("F:/lucene/index01"));
            // 2:创建IndexWriter
            IndexWriterConfig config = new IndexWriterConfig(Version.LUCENE_35,
                    new StandardAnalyzer(Version.LUCENE_35));
            writer = new IndexWriter(directory, config);
            // 3:Document对象（表）
            Document doc = null;



            // 4:为Document添加Field(表的字段)
            // 该文件夹下有文档txt等文档
            File f = new File("F:/lucene/example");
            for (File file : f.listFiles()) {
                doc = new Document();
                doc.add(new Field("content", new FileReader(file)));
                doc.add(new Field("filename", file.getName(), Field.Store.YES,
                        Field.Index.NOT_ANALYZED));
                doc.add(new Field("path", file.getAbsolutePath(),
                        Field.Store.YES, Field.Index.NOT_ANALYZED));
                // 5:通过IndexWriter添加文档到索引中
                writer.addDocument(doc);
            }
        } catch (CorruptIndexException e) {
            e.printStackTrace();
        } catch (LockObtainFailedException e) {
            e.printStackTrace();
        } catch (FileNotFoundException e) {
            
            e.printStackTrace();
        } catch (IOException e) {
            
            e.printStackTrace();
        } finally {
            try {
                if (writer != null) {
                    writer.close();
                }
            } catch (CorruptIndexException e) {
                
                e.printStackTrace();
            } catch (IOException e) {
                
                e.printStackTrace();
            }
        }
    }

{% endhighlight %}

###索引搜索步骤
1. 创建Directory
2. 创建IndexReader
3. 根据IndexReader创建IndexSearcher
4. 创建搜索的Query
5. 根据searcher搜索并且返回TopDocs
6. 根据TopDocs获取ScoreDoc对象
7. 根据Searcher得到搜索文档

{% highlight java %}

    /**
     * 搜索索引
     * 
     * @throws ParseException
     * @throws IOException
     */
    public void searcher() {
        IndexReader reader  = null;
        try {
            // 1:创建Directory
            Directory directory = FSDirectory
                    .open(new File("F:/lucene/index01"));
            // 2:创建IndexReader
             reader = IndexReader.open(directory);
            // 3:根据IndexReader创建IndexSearcher
            IndexSearcher searcher = new IndexSearcher(reader);
            // 4:创建搜索的Query
            // 创建parser来确定要搜索文件的内容，第二个参数表示要搜索的域
            QueryParser parser = new QueryParser(Version.LUCENE_35, "content",
                    new StandardAnalyzer(Version.LUCENE_35));
            Query query = parser.parse("java");// content里面包含java的文档
            // 5:根据searcher搜索并且返回TopDocs
            TopDocs tds = searcher.search(query, 10);// 搜索10条
            ScoreDoc[] sds = tds.scoreDocs;
            for (ScoreDoc sd : sds) {
                // 6:根据TopDocs获取ScoreDoc对象
                // 7：根据Searcher
                Document d = searcher.doc(sd.doc);
                System.out.println("path:"+d.get("path" )+"\t filename:" +d.get("filename"));
            }
        } catch (CorruptIndexException e) {
            
            e.printStackTrace();
        } catch (IOException e) {
            
            e.printStackTrace();
        } catch (ParseException e) {
            
            e.printStackTrace();
        }finally{
            if(reader!=null){
                try {
                    reader.close();
                } catch (IOException e) {
                    
                    e.printStackTrace();
                }
            }
        }
    }

{% endhighlight %}

###测试类
{% highlight java %}

/**
 * 测试类
 */
public class TestLucene {
    @Test
    public void testIndex() throws IOException {
        HelloLucene hl = new HelloLucene();
        hl.index();
    }
    @Test
    public void testSearch() {
        HelloLucene hl = new HelloLucene();
        hl.searcher();
    }
}

{% endhighlight %}


###注意：
1. 异常必须进行处理，而不是抛出异常，否则可能会产生索引无法建立。除非使用<code>write.flush()</code>进行强制刷新。