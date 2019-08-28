> SQL Server 发送邮件

refer :
https://stackoverflow.com/questions/13289446/database-email-with-attachementexcel-file-pdf-file

https://blog.csdn.net/cniaomingjian/article/details/7902837

result :

SET ANSI_NULLS ON;
GO
SET QUOTED_IDENTIFIER ON;
GO

IF OBJECT_ID('PROC_AEG_SendAgencyEmail') IS NOT NULL
    DROP PROCEDURE PROC_AEG_SendAgencyEmail;
GO
-- =============================================
-- Author:		chen
-- Create date: 20190827
-- Description:	招商加盟信息导出并发送Email
-- =============================================
CREATE PROC [dbo].[PROC_AEG_SendAgencyEmail]
    @DepartmentId VARCHAR(MAX) ,
    @UserId VARCHAR(MAX) ,
    @Keyword VARCHAR(MAX) ,
    @Status VARCHAR(MAX) ,
    @CustomerType VARCHAR(MAX) ,
    @StartTime DATETIME ,
    @EndTime DATETIME ,
    @Recipients VARCHAR(MAX) ,
    @Result INT OUTPUT
AS
    DECLARE @sql VARCHAR(MAX);
    BEGIN 
        SET NOCOUNT ON;
        SET @sql = '
		SET NOCOUNT ON;
		SELECT  t.F_Contact [客户名称] ,
				t.F_Mobile [手机] ,
				t.F_Address [店铺地址] ,
				t.F_Source [来源] ,
				t.F_AreaSize [店面面积] ,
				t.F_StoreType [店铺位置] ,
				t.F_Maturity [意向等级] ,
				(CASE t.F_Status WHEN 0 THEN ''待跟进'' WHEN 1 THEN ''跟进中'' WHEN 2 THEN ''已终结'' WHEN 3 THEN ''已签约''  END) [客户跟进状态] ,
				t.F_UserName [跟进人] ,
				CONVERT(VARCHAR(20),t.F_LastTrackDate,120) [上次跟进时间] ,
				( SELECT    F_TrackDescription
				  FROM      ( SELECT    * ,
										ROW_NUMBER() OVER ( PARTITION BY F_AgencyInfoId ORDER BY F_CreateDate DESC ) AS rank
							  FROM      AEG_Agency_Track
							) k
				  WHERE     k.rank = 1
							AND k.F_AgencyInfoId = t.F_AgencyInfoId
				) [上次跟进内容] ,
				t.F_CreateUserName [创建人] ,
				CONVERT(VARCHAR(20), t.F_CreateDate,120) [创建时间] 
				-- , (CASE t.F_Source_Type WHEN 0 THEN ''线上客服中心'' WHEN 1 THEN ''线下招商经理录入'' END) [信息来源]
		FROM    AEG_Agency_Info t
				INNER JOIN dbo.AEG_Base_User u ON t.F_UserId = u.F_UserId
		WHERE   1 = 1
				AND t.F_DeleteMark = 0 ';
        IF @DepartmentId <> ''
            BEGIN 
                SET @sql += ' AND u.F_DepartmentId = ''' + @DepartmentId  + '''';
            END;
        ELSE
            BEGIN
                IF @UserId <> ''
                    SET @sql += '  AND t.F_UserId = ''' + @UserId + '''';
            END;
  
        IF @Keyword <> ''
            BEGIN 
                SET @sql += ' AND t.F_Contact LIKE ''' + @Keyword + '''';
            END;
        IF @Status <> ''
            BEGIN 
                SET @sql += ' AND t.F_Status = ' + @Status;
            END;
        IF @CustomerType <> ''
            BEGIN 
                SET @sql += ' AND t.F_CustomerType = ''' + @CustomerType + '''';
            END;
        IF @StartTime <> ''
            AND @EndTime <> ''
            BEGIN 
                SET @sql += ' AND t.F_CreateDate BETWEEN ''' + CAST(@StartTime AS VARCHAR(200)) +  ''' AND ''' + CAST(@EndTime AS VARCHAR(200)) + '''';
            END;
    END;

    -- EXEC (@sql);

    DECLARE @tab CHAR(1) = CHAR(9);

    EXEC @result = msdb.dbo.sp_send_dbmail 
        @profile_name = 'AEG_AgencyInfo_SendMail',
        @recipients = @recipients, 
        @subject = '意向加盟信息', 
        @query = @sql,
        @execute_query_database = 'AEG_TS_System_New',
        @attach_query_result_as_file = 1, -- 将查询结果作为附件
        @query_result_header = 1,         -- 包括列头
        @query_attachment_filename = '意向加盟信息.csv',
        @query_result_separator = @tab,   -- 数据分隔符,使用tab才能被识别为 CSV 格式在Excel中打开
        @query_result_no_padding = 1;     -- 去除padding
