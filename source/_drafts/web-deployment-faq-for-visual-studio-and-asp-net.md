---
title: 'Web_Deployment_FAQ_for_Visual_Studio_and_ASP.NET '
tags:
---
## Can I exclude specific files or folders from deployment?
You can limit the files that are deployed by selecting the Only files needed to run this application or All files in this project options on the Package/Publish Web tab. If you select the All files in this project option, you can right-click a file in Solution Explorer and select Exclude From Project to keep it from being deployed. For more information about what files are excluded when you use the Only files needed to run this application or All files in this project options, see Why don't all of the files in my project folder get deployed?.

If these options are not flexible enough for you, another option is to edit the .pubxml or the .wpp.targets file and add an ExcludeFilesFromDeployment element or an ExcludeFoldersFromDeployment element (or both) in the PropertyGroup element. In each element, you can specify a single name, or you can specify multiple names delimited by semicolons (;), as shown in the following example:

Another option is to use the PublishIgnore NuGet package. This option is explained in Web Publishing a simpler way to exclude files/folders from being published on the .NET Web Development and Tools blog.
~~~
<PropertyGroup">
  <ExcludeFilesFromDeployment>
    File1.aspx;File2.aspx
  </ExcludeFilesFromDeployment>
  <ExcludeFoldersFromDeployment>
    Folder1;Folder2
  </ExcludeFoldersFromDeployment>
</PropertyGroup> 
~~~
For more information, see the following posts on Sayed Hashimi's blog:

1. Web Deployment Tool (MSDeploy): Build Package including extra files or excluding specific files

2. Web Deployment Tool (MSDeploy): How to exclude files from package based on Configuration