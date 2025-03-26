Enter file contents hereUSE [TimeBooking_Dev]
GO

/****** Object:  StoredProcedure [dbo].[Get_TimebookingdetailsList_Approval]    Script Date: 26-03-2025 11:11:10 ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO









--Get_TimebookingdetailsList_Approval '950147','2024-04-08T03:30:00.000Z','2024-04-14T18:29:59.999Z','PM','950147'
ALTER PROCEDURE [dbo].[Get_TimebookingdetailsList_Approval]                         
@Emp_ID INT,                     
@FromDate Datetime,                    
@ToDate Datetime,    
@Type varchar(20),    
@ID int    
AS                        
BEGIN 
   IF (@Type LIKE '%DH%' )  
   BEGIN
      -- Logic: if employee number is in budget table wbsresponsibleid or projectpersonresponsible and in mst-dephead empno
      IF EXISTS (
         SELECT 1 
         FROM Budget TB
         INNER JOIN MST_DeptHead MD ON MD.EmployeeNo = @ID
         WHERE (((TB.WBSPersonResponsible IS NULL OR TB.WBSPersonResponsible = 0) AND TB.ProjectPersonResponsible = @ID )
         OR (Tb.WBSPersonResponsible = @ID ))
      )
      BEGIN
	  print 1
         SELECT DISTINCT(TB.TimeBooking_Id),                
         TB.Date,                
         TB.ProjectNo,                
         TB.WBSNo,                
         TB.PO,                        
         TB.BookedOn,                
         TB.Remark,                
         TB.Emp_ID,                
         TB.IsRejected,                
         TB.IsSubmitted,                               
         TB.TruncateTime,                
         TB.WBSInfoId,                
         TB.RejectedBy,                
         TB.ProjWBSInfoId,                
         TB.IsApproved,                
         TB.DHRemark,                
         TB.DHApproval,                
         TB.PMRemark,                
         TB.PMApproval,                
         TB.ApprovedByDH,                
         TB.ApprovedByPM,                
         TB.DH_ApprovalDate,                
         TB.PM_ApprovalDate,                
         TB.CreatedBy,                
         TB.CreatedOn,                
         TB.ModifiedBy,                
         TB.ModifiedOn, 
         TB.EmployeeName,
         pb.ProjectDescription AS Project_Description, 
         pb.WBSAvailableBudget AS WBSAvailable_Budget,
		 pb.ProjectProfile AS ProjectProfile,
		 TB.Department AS Department
		 FROM TRN_TimeBooking tb        
         INNER JOIN TRN_BookedDate tbd ON tb.TimeBooking_Id = tbd.TimeBooking_Id
         INNER JOIN MST_DeptHead MD ON TRIM(MD.Department) = TRIM(TB.Department)   
         INNER JOIN Budget PB ON TRIM(PB.Projectcode) = TRIM(TB.ProjectNo)  and PB.WBSnumber=tb.WBSNo          
         --WHERE TB.FromDate >= DATEADD(DAY, 0, @FromDate) AND TB.ToDate <= DATEADD(DAY, 0, @ToDate) 
		 WHERE Cast(TB.FromDate as date) >= CAST( @FromDate AS DATE) AND Cast(TB.ToDate as date) <= CAST(@ToDate AS DATE) 
         AND MD.EmployeeNo = @ID  AND (TB.IsSubmitted = 1 AND TB.IsRejected = 0 )
         AND  CONCAT(TB.ProjectNo,TB.WBSNo) NOT IN
		 (
            SELECT CONCAT(BD.Projectcode,BD.WBSnumber)
            FROM Budget BD
            WHERE BD.ProjectCode = TB.ProjectNo and BD.WBSnumber=tb.WBSNo
            AND  (((BD.WBSPersonResponsible IS NULL OR BD.WBSPersonResponsible = 0) AND BD.ProjectPersonResponsible = @ID )
			OR (BD.WBSPersonResponsible = @ID ))
         )
		 order by tb.DHApproval asc
      END

      ELSE
      BEGIN
	  print 2
          SELECT DISTINCT(TB.TimeBooking_Id),                
         TB.Date,                
         TB.ProjectNo,                
         TB.WBSNo,                
         TB.PO,                        
         TB.BookedOn,                
         TB.Remark,                
         TB.Emp_ID,                
         TB.IsRejected,                
         TB.IsSubmitted,                               
         TB.TruncateTime,                
         TB.WBSInfoId,                
         TB.RejectedBy,                
         TB.ProjWBSInfoId,                
         TB.IsApproved,                
         TB.DHRemark,                
         TB.DHApproval,                
         TB.PMRemark,                
         TB.PMApproval,                
         TB.ApprovedByDH,                
         TB.ApprovedByPM,                
         TB.DH_ApprovalDate,                
         TB.PM_ApprovalDate,                
         TB.CreatedBy,                
         TB.CreatedOn,                
         TB.ModifiedBy,                
         TB.ModifiedOn, 
         TB.EmployeeName,
         pb.ProjectDescription AS Project_Description, 
         pb.WBSAvailableBudget AS WBSAvailable_Budget,
		 pb.ProjectProfile AS ProjectProfile,
		 TB.Department AS Department
         FROM TRN_TimeBooking tb        
         INNER JOIN TRN_BookedDate tbd ON tb.TimeBooking_Id = tbd.TimeBooking_Id
         INNER JOIN MST_DeptHead MD ON TRIM(MD.Department) = TRIM(TB.Department)   
         INNER JOIN Budget PB ON TRIM(PB.Projectcode) = TRIM(TB.ProjectNo)   and PB.WBSnumber=tb.WBSNo          
         --WHERE TB.FromDate >= DATEADD(DAY, 0, @FromDate) AND TB.ToDate <= DATEADD(DAY, 0, @ToDate) 
		 WHERE Cast(TB.FromDate as date) >= CAST( @FromDate AS DATE) AND Cast(TB.ToDate as date) <= CAST(@ToDate AS DATE) 
         AND MD.EmployeeNo = @ID  AND (TB.IsSubmitted = 1 AND TB.IsRejected = 0 )
		 order by tb.DHApproval asc
      END
   END 
   ELSE IF @Type = 'PM'
      BEGIN
		
         SELECT DISTINCT(TB.TimeBooking_Id),                
         TB.Date,                
         TB.ProjectNo,                
         TB.WBSNo,                
         TB.PO,                           
         TB.BookedOn,                
         TB.Remark,                
         TB.Emp_ID,                
         TB.IsRejected,                
         TB.IsSubmitted,                            
         TB.TruncateTime,                
         TB.WBSInfoId,                
         TB.RejectedBy,                
         TB.ProjWBSInfoId,                
         TB.IsApproved,                
         TB.DHRemark,                
         TB.DHApproval,                
         TB.PMRemark,                
         TB.PMApproval,                
         TB.ApprovedByDH,                
         TB.ApprovedByPM,                
         TB.DH_ApprovalDate,                
         TB.PM_ApprovalDate,                
         TB.CreatedBy,                
         TB.CreatedOn,                
         TB.ModifiedBy,                
         TB.ModifiedOn,
         TB.EmployeeName,
         pb.ProjectDescription AS Project_Description,
         pb.WBSAvailableBudget AS WBSAvailable_Budget,
		 pb.ProjectProfile AS ProjectProfile,
		 TB.Department AS Department
         FROM TRN_TimeBooking tb                             
         INNER JOIN TRN_BookedDate tbd ON tb.TimeBooking_Id = tbd.TimeBooking_Id    
         INNER JOIN Budget PB ON TRIM(pb.Projectcode) = TRIM(tb.ProjectNo) AND TRIM(pb.WBSnumber) = TRIM(tb.WBSNo)
		 LEFT OUTER JOIN MST_DeptHead DH ON 
		 (DH.EmployeeNo=case when pb.WBSPersonResponsible IS NULL OR pb.WBSPersonResponsible = 0 THEN pb.ProjectPersonResponsible 
         ELSE pb.WBSPersonResponsible END or DH.EmployeeNo=tb.Emp_ID) and DH.Department=tb.Department
        -- WHERE TB.FromDate >= DATEADD(DAY, 0, @FromDate) 
         --AND TB.ToDate <= DATEADD(DAY, 0, @ToDate) 
		 WHERE Cast(TB.FromDate as date) >= CAST( @FromDate AS DATE) AND Cast(TB.ToDate as date) <= CAST(@ToDate AS DATE) 
		 AND (TB.IsSubmitted = 1 AND TB.IsRejected = 0 )
         AND tb.DHApproval = case when DH.EmployeeNo IS NULL THEN 1 ELSE 0 END
		 AND (((pb.WBSPersonResponsible IS NULL OR pb.WBSPersonResponsible = 0) AND pb.ProjectPersonResponsible = @ID )
         OR (pb.WBSPersonResponsible = @ID ))

		 UNION ALL

		 SELECT DISTINCT(TB.TimeBooking_Id),                
         TB.Date,                
         TB.ProjectNo,                
         TB.WBSNo,                
         TB.PO,                           
         TB.BookedOn,                
         TB.Remark,                
         TB.Emp_ID,                
         TB.IsRejected,                
         TB.IsSubmitted,                            
         TB.TruncateTime,                
         TB.WBSInfoId,                
         TB.RejectedBy,                
         TB.ProjWBSInfoId,                
         TB.IsApproved,                
         TB.DHRemark,                
         TB.DHApproval,                
         TB.PMRemark,                
         TB.PMApproval,                
         TB.ApprovedByDH,                
         TB.ApprovedByPM,                
         TB.DH_ApprovalDate,                
         TB.PM_ApprovalDate,                
         TB.CreatedBy,                
         TB.CreatedOn,                
         TB.ModifiedBy,                
         TB.ModifiedOn,
         TB.EmployeeName,
         pb.ProjectDescription AS Project_Description,
         pb.WBSAvailableBudget AS WBSAvailable_Budget,
		 pb.ProjectProfile AS ProjectProfile,
		 TB.Department AS Department
         FROM TRN_TimeBooking tb                             
         INNER JOIN TRN_BookedDate tbd ON tb.TimeBooking_Id = tbd.TimeBooking_Id    
         INNER JOIN Budget PB ON TRIM(pb.Projectcode) = TRIM(tb.ProjectNo) AND TRIM(pb.WBSnumber) = TRIM(tb.WBSNo)
		 LEFT OUTER JOIN MST_DeptHead DH ON 
		 DH.EmployeeNo=tb.Emp_ID and DH.Department=tb.Department
         --WHERE TB.FromDate >= DATEADD(DAY, 0, @FromDate) 
         --AND TB.ToDate <= DATEADD(DAY, 0, @ToDate) 
		 WHERE Cast(TB.FromDate as date) >= CAST( @FromDate AS DATE) AND Cast(TB.ToDate as date) <= CAST(@ToDate AS DATE) 
		 AND (TB.IsSubmitted = 1 AND TB.IsRejected = 0 )
         AND tb.DHApproval = 1
		 AND (((pb.WBSPersonResponsible IS NULL OR pb.WBSPersonResponsible = 0) AND pb.ProjectPersonResponsible = @ID )
         OR (pb.WBSPersonResponsible = @ID )) order by tb.PM_ApprovalDate asc
      END
   END


        
		



GO


