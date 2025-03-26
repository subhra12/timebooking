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
