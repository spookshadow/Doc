# sqlserver 时间差转换为天时分秒


DECLARE @starttime DATETIME = '2016-12-01' ,
    @endtime DATETIME = '2016-12-02 14:56:39.927';
DECLARE @datediffss INT = DATEDIFF(ss, @starttime, @endtime);
SELECT  @starttime ,
        @endtime;
SELECT  CAST(CAST(@datediffss / (60*60*24) AS INT) AS VARCHAR) + '天'
        + CAST(CAST(@datediffss % 86400 / 3600 AS INT) AS VARCHAR) + '小时'
        + CAST(CAST(@datediffss % 3600 / 60 AS INT) AS VARCHAR) + '分'
        + CAST(CAST(@datediffss % 60 AS INT) AS VARCHAR) + '秒' AS 时间差; 
