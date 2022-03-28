---
layout: post
title:  PowerDesigner中Name和Comment转换
categories: [PowerDesigner]
excerpt: 在使用PowerDesigner对数据库进行概念模型和物理模型设计时，一般在Name或Comment中写中文，在Code中写英文。Name用来显示，Code对应数据库中的列名，Comment中的文字会保存到数据库Table或Column的Comment中，当Name已经存在的时候，再写一次 Comment很麻烦，可以使用以下代码来解决这个问题。
---

在使用PowerDesigner对数据库进行概念模型和物理模型设计时，一般在Name或Comment中写中文，在Code中写英文。Name用来显示，Code对应数据库中的列名，Comment中的文字会保存到数据库Table或Column的Comment中，当Name已经存在的时候，再写一次 Comment很麻烦，可以使用以下代码来解决这个问题。
另外在使用REVERSE ENGINEER从数据库反向生成PDM的时候，PDM中的表的Name和Code事实上都是Code，为了把Name替换为数据库中Table或Column的中文Comment，可以使用以下脚本。

#### 将Name中的内容复制至Comment中
```bash
Option   Explicit
ValidationMode   =   True
InteractiveMode   =   im_Batch

Dim   mdl   '   the   current   model

'   get   the   current   active   model
Set   mdl   =   ActiveModel
If   (mdl   Is   Nothing)   Then
      MsgBox   "There   is   no   current   Model "
ElseIf   Not   mdl.IsKindOf(PdPDM.cls_Model)   Then
      MsgBox   "The   current   model   is   not   an   Physical   Data   model. "
Else
      ProcessFolder   mdl
End   If

'   This   routine   copy   name   into   comment   for   each   table,   each   column   and   each   view
'   of   the   current   folder
Private   sub   ProcessFolder(folder)
      Dim   Tab   'running     table
      for   each   Tab   in   folder.tables
            if   not   tab.isShortcut   then
                  tab.comment   =   tab.name
                  Dim   col   '   running   column
                  for   each   col   in   tab.columns
                        col.comment=   col.name
                  next
            end   if
      next

      Dim   view   'running   view
      for   each   view   in   folder.Views
            if   not   view.isShortcut   then
                  view.comment   =   view.name
            end   if
      next

      '   go   into   the   sub-packages
      Dim   f   '   running   folder
      For   Each   f   In   folder.Packages
            if   not   f.IsShortcut   then
                  ProcessFolder   f
            end   if
      Next
end   sub
```

#### 将Comment中的内容复制至Name中
```bash
Option   Explicit
ValidationMode   =   True
InteractiveMode   =   im_Batch

Dim   mdl   '   the   current   model

'   get   the   current   active   model
Set   mdl   =   ActiveModel
If   (mdl   Is   Nothing)   Then
      MsgBox   "There   is   no   current   Model "
ElseIf   Not   mdl.IsKindOf(PdPDM.cls_Model)   Then
      MsgBox   "The   current   model   is   not   an   Physical   Data   model. "
Else
      ProcessFolder   mdl
End   If

Private   sub   ProcessFolder(folder)
On Error Resume Next
      Dim   Tab   'running     table
      for   each   Tab   in   folder.tables
            if   not   tab.isShortcut   then
                  tab.name   =   tab.comment
                  Dim   col   '   running   column
                  for   each   col   in   tab.columns
                  if col.comment="" then
                  else
                        col.name=   col.comment
                  end if
                  next
            end   if
      next

      Dim   view   'running   view
      for   each   view   in   folder.Views
            if   not   view.isShortcut   then
                  view.name   =   view.comment
            end   if
      next

      '   go   into   the   sub-packages
      Dim   f   '   running   folder
      For   Each   f   In   folder.Packages
            if   not   f.IsShortcut   then
                  ProcessFolder   f
            end   if
      Next
end   sub
```

以上两段代码都是VB脚本，在PowerDesigner中使用方法为：
PowerDesigner->Tools->Execute Commands->Edit/Run Scripts
将代码Copy进去执行就可以了，是对整个CDM或PDM进行操作
