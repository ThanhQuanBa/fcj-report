---
title: "Blog 3"
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Giải pháp tìm kiếm và cơ sở dữ liệu có khả năng mở rộng, đàn hồi cho hơn 1 tỷ vectơ được xây dựng trên LanceDB và Amazon S3

bởi Audra Devoto, Christopher Brown, Patrick O'Connor, Owen Janson và Pavel Novichkov trên22 THÁNG 9 NĂM 2025 trong [Nâng cao (300)](https://aws.amazon.com/blogs/architecture/category/learning-levels/advanced-300/) , [Dịch vụ lưu trữ đơn giản của Amazon (S3)](https://aws.amazon.com/blogs/architecture/category/storage/amazon-simple-storage-services-s3/) , [AWS Batch](https://aws.amazon.com/blogs/architecture/category/compute/aws-batch/) , [AWS Lambda](https://aws.amazon.com/blogs/architecture/category/compute/aws-lambda/) , [Chức năng bước AWS](https://aws.amazon.com/blogs/architecture/category/application-services/aws-step-functions/) , [Giải pháp khách hàng](https://aws.amazon.com/blogs/architecture/category/post-types/customer-solutions/) , [Đổi mới và tái tạo](https://aws.amazon.com/blogs/architecture/category/enterprise-strategy/innovation-and-reinvention/) , [Khoa học đời sống](https://aws.amazon.com/blogs/architecture/category/industries/life-sciences/) , [Không máy chủ](https://aws.amazon.com/blogs/architecture/category/serverless/) , [Khởi](https://aws.amazon.com/blogs/architecture/category/startup/) nghiệp , [Hướng dẫn kỹ thuật Liên kết](https://aws.amazon.com/blogs/architecture/category/post-types/technical-how-to/) [cố định](https://aws.amazon.com/blogs/architecture/a-scalable-elastic-database-and-search-solution-for-1b-vectors-built-on-lancedb-and-amazon-s3/) [Bình luận](https://aws.amazon.com/blogs/architecture/a-scalable-elastic-database-and-search-solution-for-1b-vectors-built-on-lancedb-and-amazon-s3/#Comments) [Chia sẻ](https://aws.amazon.com/vi/blogs/architecture/a-scalable-elastic-database-and-search-solution-for-1b-vectors-built-on-lancedb-and-amazon-s3/#)

_Bài viết này được đồng sáng tác với Owen Janson, Audra Devoto và Christopher Brown của Metagenomi._

Từ chỉnh sửa gen [CRISPR](https://innovativegenomics.org/what-is-crispr/) đến xúc tác sinh học công nghiệp, enzyme cung cấp năng lượng cho một số công nghệ mang tính đột phá nhất trong chăm sóc sức khỏe, năng lượng và sản xuất. Tuy nhiên, việc phát hiện ra các enzyme mới có khả năng chuyển đổi cả một ngành công nghiệp — chẳng hạn như Cas9 cho kỹ thuật di truyền gen — đòi hỏi phải sàng lọc hàng tỷ enzyme đa dạng được mã hóa bởi các sinh vật trải dài trên cây sự sống. Những tiến bộ trong giải trình tự DNA và metagenomics đã cho phép phát triển các cơ sở dữ liệu công cộng và độc quyền khổng lồ chứa các trình tự protein đã biết, nhưng việc rà soát các bộ sưu tập này để xác định các ứng cử viên có giá trị cao về cơ bản là một vấn đề về dữ liệu lớn cũng như sinh học.

Tại [Metagenomi](https://metagenomi.co/) , chúng tôi đang phát triển các liệu pháp có tiềm năng chữa bệnh bằng cách sử dụng cơ sở dữ liệu metagenomics (MGXdb) rộng lớn của mình để xây dựng một bộ công cụ gồm các hệ thống chỉnh sửa gen mới. Trong bài viết này, chúng tôi sẽ nêu bật cách Metagenomi giải quyết thách thức khám phá enzyme ở quy mô hàng tỷ protein bằng cách sử dụng cơ sở hạ tầng có khả năng mở rộng của [Amazon Web Services](https://aws.amazon.com/aws/) (AWS) để xây dựng cơ sở dữ liệu protein hiệu suất cao và giải pháp tìm kiếm dựa trên nhúng. Bằng cách nhúng mọi protein trong cơ sở dữ liệu độc quyền lớn của chúng tôi vào một không gian vector, cho phép dữ liệu có thể truy cập bằng [LanceDB](https://lancedb.github.io/lancedb/) được xây dựng trên [Amazon Simple Storage Service](https://aws.amazon.com/s3/) (Amazon S3) và được truy cập bằng [AWS Lambda](https://aws.amazon.com/lambda/) , chúng tôi đã có thể biến việc khám phá enzyme thành bài toán tìm kiếm lân cận gần nhất và nhanh chóng tiếp cận không gian khám phá chưa được khám phá trước đây.

## **Tổng quan về giải pháp**

Cốt lõi của giải pháp của chúng tôi là LanceDB. LanceDB là một cơ sở dữ liệu vector nguồn mở cho phép tìm kiếm xấp xỉ lân cận gần nhất (ANN) nhanh chóng trên các vector được lập chỉ mục. LanceDB đặc biệt phù hợp với ngăn xếp không máy chủ vì nó hoàn toàn dựa trên tệp và cũng tương thích với lưu trữ Amazon S3. Do đó, chúng tôi có thể lưu trữ cơ sở dữ liệu trình tự protein nhúng trên Amazon S3 với chi phí tương đối thấp, thay vì lưu trữ đĩa cố định như [Amazon Elastic Block Store](https://aws.amazon.com/ebs/) (Amazon EBS). Thay vì máy chủ chạy liên tục, tất cả những gì cần thiết để truy vấn cơ sở dữ liệu theo yêu cầu nhanh chóng là một hàm Lambda sử dụng LanceDB để tìm lân cận gần nhất trực tiếp từ dữ liệu trên S3.

Để vượt qua thách thức trong việc thu thập và truy vấn hàng tỷ vector nhúng đại diện cho cơ sở dữ liệu protein lớn của Metagenomi, chúng tôi đã thiết kế một phương pháp chia cơ sở dữ liệu thành các phần có kích thước bằng nhau (thư mục) được lưu trữ với chi phí thấp trên Amazon S3, có thể được lập chỉ mục song song và tìm kiếm bằng phương pháp map-reduce sử dụng Lambda. Sơ đồ sau minh họa kiến ​​trúc này.

![Kiến trúc AWS hiển thị quy trình xử lý vectơ protein với ECR, Lambda và LanceDB]![alt text](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2025/08/28/lance-arch-prototype.png)]

Quá trình này diễn ra theo bốn bước:

1. Vector hóa dữ liệu

2. Phân loại dữ liệu

3. Lập chỉ mục và thu thập dữ liệu

4. Truy vấn cơ sở dữ liệu

## **Vector hóa dữ liệu**

Để sử dụng khả năng tìm kiếm ANN nhanh của LanceDB, dữ liệu phải ở dạng vector. Cơ sở dữ liệu metagenomics của chúng tôi bao gồm hàng tỷ protein, mỗi protein là một chuỗi axit amin. Để chuyển đổi mỗi protein thành một vector nắm bắt thông tin có ý nghĩa sinh học, chúng tôi chạy chúng qua một mô hình ngôn ngữ protein (pLM), nắm bắt các lớp ẩn của mô hình như một biểu diễn vector của protein đó. Nhiều pLM có thể được sử dụng để tạo nhúng protein, tùy thuộc vào thông tin sinh học mong muốn và các yêu cầu tính toán. Ở đây, chúng tôi sử dụng [mô hình AMPLIFY_350M](https://github.com/chandar-lab/AMPLIFY) , một mô hình mã hóa biến áp đủ nhanh để mở rộng quy mô cho toàn bộ cơ sở dữ liệu protein của chúng tôi. Chúng tôi thực hiện một nhóm trung bình của lớp ẩn cuối cùng của mô hình để tạo ra một vector 960 chiều cho mỗi protein. Các vector này và ID protein duy nhất tương ứng của chúng sau đó được lưu trữ trong các tệp HDF5.

## **Phân loại dữ liệu**

Để chuyển đổi các vectơ protein thành cơ sở dữ liệu có thể tìm kiếm, chúng tôi sử dụng LanceDB để xây dựng một chỉ mục phù hợp cho việc nhanh chóng tìm kiếm các mạng nơ-ron nhân tạo (ANN) cho một truy vấn. Tuy nhiên, việc lập chỉ mục có thể mất nhiều thời gian và khó phân bổ trên các nút. Để tăng tốc độ lập chỉ mục, trước tiên chúng tôi chia dữ liệu thành các nhóm có kích thước gần bằng nhau. Sau đó, chúng tôi gán mỗi tệp HDF5 nhúng vào các nhóm có kích thước xấp xỉ bằng 200 triệu vectơ tổng cộng bằng thuật toán đóng gói bin phù hợp nhất. Phương pháp đóng gói kích thước chính xác được sử dụng để đóng gói dữ liệu phụ thuộc vào số lượng và kích thước của các vectơ, cũng như định dạng của chúng. Mỗi nhóm được nhập vào một bảng riêng biệt, bảng này sẽ nằm riêng trong một kho lưu trữ đối tượng cơ sở dữ liệu LanceDB duy nhất trên Amazon S3.

![Cấu trúc thùng S3 hiển thị tổ chức cơ sở dữ liệu LanceDB với các thùng vector]![alt text](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2025/08/28/lance-bucket-breakdown.png)]

Bằng cách phân nhóm dữ liệu, chúng ta có thể tạo ra nhiều cơ sở dữ liệu nhỏ hơn, có thể được lập chỉ mục trên các nút riêng biệt trong thời gian ngắn hơn nhiều. Chúng ta cũng có thể thêm dữ liệu vào cơ sở dữ liệu theo từng bước dưới dạng một nhóm mới, thay vì phải lập chỉ mục lại toàn bộ dữ liệu hiện có.

## **Thu thập và lập chỉ mục dữ liệu được phân loại**

Sau khi dữ liệu vector hóa đã được gán vào một bucket, đã đến lúc chuyển đổi nó thành một bảng LanceDB và lập chỉ mục để cho phép truy vấn ANN nhanh chóng. Chi tiết về cách chuyển đổi dữ liệu cụ thể của bạn thành bảng LanceDB có thể được tìm thấy trong [tài liệu LanceDB](https://lancedb.github.io/lancedb/) . Đối với mỗi bucket của chúng tôi, với khoảng 200 triệu vector, chúng tôi tạo một bảng LanceDB với chỉ mục IVF-PQ theo khoảng cách cosin. Để lập chỉ mục, chúng tôi sử dụng một số phân vùng bằng căn bậc hai của số hàng được chèn và một số vector con bằng số chiều của các vector chia cho 16\.

Để việc truy vấn trở nên dễ dàng hơn, chúng tôi đặt tên cho mỗi bảng theo tên thùng mà bảng được tạo ra và tải chúng lên một thư mục S3 duy nhất sao cho cấu trúc tệp của chúng chỉ ra một cơ sở dữ liệu LanceDB duy nhất với nhiều bảng.

Đoạn mã sau đây cung cấp một ví dụ về cách bạn có thể nhập các vectơ từ tệp HDF5 chứa các cột idvà embeddingvào cơ sở dữ liệu LanceDB và lập chỉ mục để tìm kiếm ANN nhanh dựa trên khoảng cách cosin. Yêu cầu duy nhất để chạy đoạn mã này là python \>= 3.9, cũng như các gói lancedb, pyarrow, và h5py. Cần lưu ý rằng đoạn mã này đã được thử nghiệm và phát triển bằng cách sử dụng lancedbphiên bản 0.21.1sử dụng API LanceDB không đồng bộ.

from typing import List, Iterable

from itertools import islice

from math import sqrt

import pyarrow as pa

import datetime

import asyncio

import lancedb

import h5py

def batched(iterable: Iterable, n: int) \-\> Iterable\[List\]:

    """Yield batches of n items from iterable."""

    while batch := list(islice(iterable, n)):

        yield batch

async def vectors_to_db(

    vectors: str,

    db: str,

    table\_name: str,

    vector\_dim: int,

    ingestion\_batch\_size: int,

) \-\> int:

    """Ingest and index vectors from an HDF5 file into a LanceDB table.

    Args:

        vectors (str): An HDF5 file containing protein IDs and their

            960-dimension vector representations.

        db (str): Path to the LanceDB database.

        table\_name (str): Name of the table to create.

        vector\_dim (int): Dimension of the vectors.

    """

    \# create db and table

    custom\_schema \= pa.schema(

        \[

            pa.field("embedding", pa.list\_(pa.float32(), vector\_dim)),

            pa.field("id", pa.string()),

        \]

    )

    \# count the total number of rows as they are added to the table

    total\_rows \= 0

    \# open a connection to the new database and create a table

    with await lancedb.connect\_async(db) as db\_connection:

        with await db\_connection.create\_table(

            table\_name, schema\=custom\_schema

        ) as table\_connection:

            \# open vectors file

            with h5py.File(vectors, "r") as vectors\_handle:

                \# create a generator over the rows

                rows \= (

                    {"embedding": e, "id": i}

                    for e, i in zip(

                        vectors\_handle\["embedding"\],

                        vectors\_handle\["id"\],

                    )

                )

                \# insert rows in batches to avoid memory issues

                for batch in batched(rows, ingestion\_batch\_size):

                    total\_rows \+= len(batch)

                    await table\_connection.add(batch)

            \# optimize the table and remove old data

            await table\_connection.optimize(

                cleanup\_older\_than\=datetime.timedelta(days\=0)

            )

            \# configure the index for the table

            index\_config \= lancedb.index.IvfPq(

                distance\_type\="cosine",

                num\_partitions\=int(sqrt(total\_rows)),

                num\_sub\_vectors\=int(

                    vector\_dim / 16

                ),

            )

            \# index the table

            await table\_connection.create\_index(

                "embedding", config\=index\_config

            )

\# ingest and index your data

asyncio.run(

    vectors\_to\_db(

        vectors\="./my\_vectors.h5",

        db\="./test\_db",

        table\_name\="bucket1",

        vector\_dim\=960,

        ingestion\_batch\_size\=50000

    )

)

Nhiệm vụ vector hóa, thu thập, lập chỉ mục cho từng thùng có thể được thực hiện song song trên nhiều tác vụ [AWS Batch](https://aws.amazon.com/batch/) hoặc chạy trên một phiên bản [Amazon Elastic Compute Cloud](https://aws.amazon.com/ec2/) (Amazon EC2) duy nhất.

## **Truy vấn cơ sở dữ liệu**

Sau khi dữ liệu được phân nhóm và nhập vào cơ sở dữ liệu LanceDB trên Amazon S3, chúng ta cần một cách để truy vấn dữ liệu. Vì LanceDB có thể được truy vấn trực tiếp từ Amazon S3 bằng API Python của LanceDB, chúng ta có thể sử dụng các hàm Lambda để lấy một vectơ truy vấn do người dùng cung cấp và tìm kiếm các mạng nơ-ron nhân tạo (ANN), sau đó trả về dữ liệu cho người dùng. Tuy nhiên, vì dữ liệu của chúng ta đã được phân nhóm trên nhiều bảng trong cơ sở dữ liệu, chúng ta cần tìm kiếm các lân cận gần nhất trong mỗi nhóm và tổng hợp kết quả trước khi trả về cho người dùng.

Chúng tôi triển khai quy trình truy vấn dưới dạng [máy trạng thái](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-statemachines.html) [AWS Step Functions](https://aws.amazon.com/step-functions/) , quản lý quy trình truy vấn cho mỗi bucket dưới dạng quy trình Lambda, cũng như một quy trình Lambda duy nhất ở cuối quy trình, tổng hợp dữ liệu và ghi các ANN kết quả vào tệp .csv trên Amazon S3. Tuy nhiên, quy trình này cũng có thể được triển khai dưới dạng một loạt quy trình AWS Batch hoặc thậm chí chạy cục bộ. Đoạn mã sau đây cho thấy cách một quy trình được gán cho một bucket có thể chạy truy vấn ANN trên một trong các bucket của cơ sở dữ liệu, chỉ yêu cầu và chạy trên . Như đã trình bày chi tiết trước đó trong phần thu thập dữ liệu, chúng tôi sử dụng API LanceDB không đồng bộ và phiên bản gói .pandaslancedbpython \>= 3.9lancedb0.21.1

from typing import List, Iterable

import asyncio

import lancedb

import pandas

import random

async def run_query_async(

    lancedb\_s3\_uri: str,

    table\_name: str,

    q\_vec: List\[float\],

    k: int,

    vec\_col: str,

    n\_probes: int,

    refine\_factor: int,

) \-\> pandas.DataFrame:

    """Run a query on a LanceDB table.

    Args:

        lancedb\_s3\_uri (str): S3 URI of the LanceDB database.

        table\_name (str): Name of the table to query.

        q\_vec (List\[float\]): Query vector.

        k (int): Number of nearest neighbors to return.

        vec\_col (str): Column name of the vector column.

        n\_probes (int): Number of probes to use for the query.

        refine\_factor (int): Refine factor for the query.

    Returns:

        pandas.DataFrame: DataFrame containing the approximate nearest

        neighbors to the query vector.

    """

    \# open a connection to the database and table

    with await lancedb.connect\_async(

        lancedb\_s3\_uri, storage\_options\={"timeout": "120s"}

    ) as db\_connection:

        with await db\_connection.open\_table(table\_name) as table\_connection:

            \# query the approximate nearest neighbors to the query vector

            df \= (

                await table\_connection.query()

                .nearest\_to(q\_vec)

                .column(vec\_col)

                .nprobes(n\_probes)

                .refine\_factor(refine\_factor)

                .limit(k)

                .distance\_type("cosine")

                .to\_pandas()

            )

    return df

\# query the example bucket we produced in the last section

bucket1_df \= asyncio.run(

    snippets.run\_query\_async(

        lancedb\_s3\_uri\="s3://mg-analysis/owen/20250415\_lancedb\_snippet\_testing/test\_db/",

        table\_name\="bucket1",

        q\_vec\=\[random.random() for \_ in range(960)\],

        k\=3,

        vec\_col\="embedding",

        n\_probes\=1,

        refine\_factor\=1,

    )

)

Truy vấn trước đó sẽ trả về một panda DataFrame có cấu trúc sau:

| nhúng                      | nhận dạng | \_khoảng cách |
| :------------------------- | :-------- | :------------ |
| \[-5.124435, 4.242000, …\] | id_1      | 0,000000      |
| \[-5,783999, 4,340500, …\] | id_2      | 0,001000      |
| \[-6,932943, 3,394850, …\] | id_3      | 0,04020       |

Trong đó, embeddingcột chứa biểu diễn vectơ của các láng giềng gần nhất, idcột chứa ID của chúng và \_distancecột chứa khoảng cách cosin của chúng đến vectơ được truy vấn.

Sau khi mỗi bucket được truy vấn độc lập trên các nút và mỗi bucket trả về một DataFrame lân cận gần nhất, kết quả phải được hợp nhất và tập hợp lại để trả về người dùng. Đoạn mã sau đây cho thấy cách bạn có thể thực hiện việc này.

def aggregate_nearest_neighbors(

    dfs: List\[pandas.DataFrame\], k: int

):

    """Aggregate the nearest neighbors for each query vector.

    Args:

        dfs (List\[pandas.DataFrame\]): A list of DataFrames containing the

            nearest neighbors queried from each bucket.

        k (int): The number of nearest neighbors to aggregate.

    Returns:

        pd.DataFrame: A DataFrame with the aggregated nearest neighbors.

    """

    \# concatenate the DataFrames and get the top k nearest neighbors

    return (

        pandas.concat(dfs, ignore\_index\=True)

        .sort\_values(by\=\["\_distance"\], ascending\=True)

        .reset\_index(drop\=True)

        .head(k)

    )

\# add the dataframes from querying each bucket to a list

dfs \= \[bucket1_df, bucket2_df, bucket3_df, bucket4_df, bucket_5\]

\# aggregate the nearest neighbors across all buckets

nearest_neighbors_all_buckets_df \= aggregate_nearest_neighbors(dfs, 5)

## **Tối ưu hóa cho các lô truy vấn lớn**

Mặc dù truy vấn cơ sở dữ liệu LanceDB trực tiếp từ kho đối tượng S3 trên Lambda hoạt động tốt khi truy vấn ANN của một hoặc một vài vectơ truy vấn, một số trường hợp sử dụng có thể yêu cầu truy vấn hàng nghìn hoặc thậm chí hàng triệu vectơ.

Một giải pháp chúng tôi tìm thấy có khả năng mở rộng tốt với các lô truy vấn lớn là sửa đổi triển khai truy vấn trước đó sao cho trước tiên tải xuống một trong các thùng cơ sở dữ liệu vào bộ nhớ cục bộ, sau đó truy vấn cục bộ bằng API LanceDB. Vì các thùng cơ sở dữ liệu có thể có dấu chân lưu trữ lớn, nên triển khai này phù hợp hơn với các tác vụ AWS Batch so với Lambda và chúng tôi khuyên bạn nên sử dụng bộ nhớ phiên bản được tối ưu hóa (ví dụ: phiên bản i4i) thay vì ổ đĩa EBS. Sau khi tất cả các tác vụ Batch truy vấn hoàn tất, một tác vụ cuối cùng có thể tổng hợp kết quả của chúng trước khi trả về cho người dùng. Việc phối hợp các tác vụ truy vấn song song và tác vụ tổng hợp có thể được thực hiện bằng [Nextflow](https://www.nextflow.io/docs/latest/index.html) . Mặc dù triển khai này sẽ có chi phí và độ trễ cao hơn đáng kể khi tải các thùng xuống đĩa, nhưng nó có thể xử lý các lô truy vấn lớn hơn một cách hiệu quả hơn và vẫn không yêu cầu cơ sở dữ liệu chạy liên tục trên máy chủ.

## **Kết quả đánh giá chuẩn**

Chiến lược lập chỉ mục và kích thước phân chia cơ sở dữ liệu phụ thuộc vào nhu cầu hiệu suất cá nhân của bạn. Hãy cân nhắc hướng dẫn tối ưu hóa chung sau đây khi tùy chỉnh theo trường hợp sử dụng của bạn.

Một cơ sở dữ liệu mẫu do Metagenomi tạo ra bao gồm 3,5 tỷ vector nhúng được tạo bởi AMPLIFY, với kích thước 960\. Việc tiếp nhận và lập chỉ mục 3,5 tỷ vector nhúng này thành các kích thước chia nhỏ, mỗi vector 200 triệu trên i4i.8xlargecác phiên bản, mất tổng cộng 108 giờ tính toán. Vì giải pháp này không cần máy chủ và có thể được truy vấn trực tiếp từ kho đối tượng S3, nên chi phí cố định duy nhất của cơ sở dữ liệu này là dung lượng lưu trữ trên Amazon S3 (đối với cơ sở dữ liệu được lập chỉ mục gồm 3,5 tỷ vector, chi phí này là khoảng 12,9 TB). Truy vấn Lambda có thể là một giải pháp truy vấn có chi phí cực kỳ thấp, với nhiều truy vấn chỉ tốn vài phần trăm.

Nhìn chung, việc phân chia cơ sở dữ liệu lớn hơn sẽ tiết kiệm chi phí truy vấn hơn nhưng sẽ dẫn đến thời gian chạy và thời gian lập chỉ mục dài hơn. Chúng tôi khuyến nghị tăng quy mô phân chia cơ sở dữ liệu lên kích thước tối đa để đạt được thời gian trả về truy vấn chấp nhận được cho một lần phân chia duy nhất, đồng thời cân nhắc các giới hạn song song hóa, chẳng hạn như số lượng hàm Lambda đồng thời tối đa đang chạy. Metagenomi đã xác định các phân chia cơ sở dữ liệu với 200 triệu vectơ mỗi lần để mang lại sự cân bằng tối ưu về chi phí và thời gian chạy cho cả truy vấn nhỏ và lớn. Chúng tôi khuyến nghị sử dụng và lập chỉ mục trên các phiên bản được tối ưu hóa lưu trữ, chẳng hạn như các phiên bản thuộc họ i4i, để đạt hiệu suất và tiết kiệm chi phí tối ưu. Nếu truy vấn được thực hiện trên một phiên bản sử dụng cơ sở dữ liệu dựa trên đĩa (khác với Lambda và Amazon S3), chúng tôi cũng khuyến nghị sử dụng các phiên bản được tối ưu hóa lưu trữ cho các truy vấn. Chúng tôi nhận thấy việc triển khai Lambda có thể nhanh chóng xử lý các truy vấn đơn lẻ yêu cầu tối đa 50.000 ANN hoặc nhiều truy vấn lên đến 100 chuỗi với ít hơn 5 ANN. Thời gian chạy tăng tuyến tính theo số lượng ANN được yêu cầu, như được hiển thị trong biểu đồ sau.

![Biểu đồ đường cho thấy thời gian chạy truy vấn tăng dần theo số lượng hàng xóm gần nhất]![alt text](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2025/08/28/metrics-graph-lance.png)]

## **Phần kết luận**

Trong bài viết này, chúng tôi đã trình bày cách Metagenomi có thể lưu trữ và truy vấn hàng tỷ protein nhúng với chi phí thấp bằng cách sử dụng LanceDB được triển khai với Amazon S3 và AWS Lambda. Công trình này mở rộng sứ mệnh hướng đến bệnh nhân của Metagenomi là tạo ra các loại thuốc di truyền chữa bệnh bằng cách đẩy nhanh nền tảng khám phá và kỹ thuật của chúng tôi. Việc truy cập nhanh vào không gian nhúng ANN của một protein truy vấn trong vài giây đã cho phép tích hợp các phương pháp tìm kiếm nhanh vào các quy trình phân tích mở rộng của chúng tôi, đẩy nhanh việc khám phá một số họ enzyme đa dạng và mới lạ, đồng thời hỗ trợ các nỗ lực kỹ thuật protein bằng cách cung cấp cho các nhà khoa học các phương pháp để tạo và tìm kiếm các nhúng một cách nhanh chóng. Khi Metagenomi tiếp tục mở rộng nhanh chóng cơ sở dữ liệu protein và DNA, việc mở rộng theo chiều ngang được hỗ trợ bởi các phân chia cơ sở dữ liệu có thể được lập chỉ mục và tìm kiếm song song tạo điều kiện cho một giải pháp cơ sở dữ liệu nhúng có thể mở rộng theo nhu cầu trong tương lai.

Giải pháp được trình bày trong bài viết này tập trung vào các vectơ được tạo ra bởi [mô hình ngôn ngữ lớn](https://aws.amazon.com/what-is/large-language-model/) protein (LLM) nhưng có thể được áp dụng cho các tập dữ liệu vectơ hóa khác. Để tìm hiểu thêm về LanceDB tích hợp với Amazon S3, vui lòng tham khảo [tài liệu LanceDB](https://lancedb.github.io/lancedb/) .

Tài liệu tham khảo

1. [Fournier, Quentin và cộng sự. “Mô hình ngôn ngữ protein: có cần thiết phải mở rộng quy mô không?.” bioRxiv (2024): 2024-09.](https://www.biorxiv.org/content/10.1101/2024.09.23.614603v1)

---

### **Về các tác giả**

<div style="display: flex; align-items: flex-start; gap: 20px; border: 1px solid #ddd; padding: 20px; border-radius: 8px;">

  <!-- ẢNH -->

<img src="https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2025/08/28/audra-devoto-metagenomi.jpeg" 
       alt="Audra Devoto" 
       width="150" 
       style="border-radius: 4px;" />

  <!-- NỘI DUNG -->
  <div style="max-width: 600px;">
    
  ### **Audra Devoto**

Audra là một nhà khoa học dữ liệu có nền tảng về metagenomics và nhiều năm kinh nghiệm làm việc với các tập dữ liệu genomics lớn trên AWS. Tại Metagenomi, cô xây dựng cơ sở hạ tầng để hỗ trợ các dự án phân tích quy mô lớn và cho phép khám phá các enzyme mới từ MGXdb.

  </div>

</div>

###

<div style="display: flex; align-items: flex-start; gap: 20px; border: 1px solid #ddd; padding: 20px; border-radius: 8px; margin-bottom: 20px;">

<img src="https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2025/08/28/chris-brown-metagenomi.jpeg" 
       alt="Christopher Brown" 
       width="150" 
       style="border-radius: 4px;" />

  <div style="max-width: 600px;">

### **Christopher Brown**

Tiến sĩ Christopher Brown là trưởng nhóm Discovery tại Metagenomi. Ông là một nhà khoa học và chuyên gia giàu kinh nghiệm trong lĩnh vực metagenomics, đồng thời là người dẫn đầu việc khám phá và mô tả đặc tính của nhiều hệ thống enzyme mới cho các ứng dụng chỉnh sửa gen.

  </div>

</div>

###

<div style="display: flex; align-items: flex-start; gap: 20px; border: 1px solid #ddd; padding: 20px; border-radius: 8px; margin-bottom: 20px;">

<img src="https://d2908q01vomqb2.cloudfront.net/fb644351560d8296fe6da332236b1f8d61b2828a/2024/04/05/Patrick-OConnor.jpeg" 
       alt="Patrick O'Connor" 
       width="150" 
       style="border-radius: 4px;" />

  <div style="max-width: 600px;">

### **Patrick O'Connor**

Patrick là Kỹ sư Nguyên mẫu AI Cấp cao Toàn cầu tại AWS, nơi anh xây dựng các giải pháp AI tạo sinh tiên tiến và các nguyên mẫu đầu cuối trên nền tảng đám mây. Anh chuyên triển khai các mô hình ngôn ngữ lớn và hệ thống AI phân tán, đồng thời vận dụng chuyên môn về IoT, công nghệ không máy chủ và điện toán hiệu năng cao để giải quyết các thách thức phức tạp của doanh nghiệp.

  </div>

</div>

###

<div style="display: flex; align-items: flex-start; gap: 20px; border: 1px solid #ddd; padding: 20px; border-radius: 8px; margin-bottom: 20px;">

<img src="https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2025/08/28/owen-janson-metagenomi.jpeg" 
       alt="Owen Janson" 
       width="150" 
       style="border-radius: 4px;" />

  <div style="max-width: 600px;">

### **Owen Janson**

Owen là Kỹ sư tin sinh học tại Metagenomi, chuyên xây dựng các công cụ và cơ sở hạ tầng đám mây để hỗ trợ phân tích các tập dữ liệu bộ gen khổng lồ.

  </div>

</div>

###

<div style="display: flex; align-items: flex-start; gap: 20px; border: 1px solid #ddd; padding: 20px; border-radius: 8px; margin-bottom: 20px;">

<img src="https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2025/09/16/Pavel.jpg" 
       alt="Pavel Novichkov" 
       width="150" 
       style="border-radius: 4px;" />

  <div style="max-width: 600px;">

### **Pavel Novichkov**

Tiến sĩ Pavel Novichkov là Kiến trúc sư Giải pháp Cao cấp tại AWS, chuyên về hệ gen và khoa học sự sống. Ông có hơn 15 năm kinh nghiệm trong lĩnh vực tin sinh học và phát triển đám mây, giúp các công ty khởi nghiệp trong lĩnh vực chăm sóc sức khỏe và khoa học sự sống thiết kế và triển khai các giải pháp đám mây trên AWS. Ông đã hoàn thành nghiên cứu sau tiến sĩ tại Trung tâm Thông tin Công nghệ Sinh học Quốc gia (NIH) và làm việc với tư cách là Nhà khoa học Nghiên cứu Tính toán tại Phòng thí nghiệm Berkeley trong hơn 12 năm, nơi ông đồng phát triển công nghệ NGS tiên tiến, được công nhận là một trong 90 đột phá hàng đầu trong lịch sử của Phòng thí nghiệm Berkeley.

  </div>

</div>
