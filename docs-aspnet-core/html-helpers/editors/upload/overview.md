---
title: Overview
page_title: Upload | Telerik UI for ASP.NET Core HtmlHelpers
description: "Learn the basics when working with the Kendo UI Upload HtmlHelper for ASP.NET Core (MVC 6 or ASP.NET Core MVC)."
previous_url: /aspnet-core/helpers/html-helpers/upload
slug: htmlhelpers_upload_aspnetcore
position: 1
---

# Upload HtmlHelper Overview

The Upload HtmlHelper extension is a server-side wrapper for the [Kendo UI Upload](https://demos.telerik.com/kendo-ui/upload/index) widget.

It enables you to configure the Kendo UI Upload widget from server-side code. The [Upload](http://docs.telerik.com/kendo-ui/controls/editors/upload/overview) uses progressive enhancement to deliver the best possible uploading experience to users, without requiring extra developer effort.

For more information on the HtmlHelper, refer to the article on the [Upload HtmlHelper for ASP.NET MVC](http://docs.telerik.com/aspnet-mvc/helpers/upload/overview).

## Basic Usage

The following example demonstrates how to define the Upload by using the Upload HtmlHelper.

###### Example

```tab-Razor
@(Html.Kendo().Upload()
    .Name("files")
    .Async(a => a
        .Save("SaveAsync", "Upload")
        .Remove("Remove", "Upload")
        .AutoUpload(true)
    )
)
```
```tab-Controller
public class UploadController : Controller
{
    public IHostingEnvironment HostingEnvironment { get; set; }

    public UploadController(IHostingEnvironment hostingEnvironment)
    {
        HostingEnvironment = hostingEnvironment;
    }

    [Demo]
    public ActionResult Async()
	{
		return View();
	}

	public async Task<ActionResult> SaveAsync(IEnumerable<IFormFile> files)
    {
        // The Name of the Upload component is "files"
        if (files != null)
        {
            foreach (var file in files)
            {
                var fileContent = ContentDispositionHeaderValue.Parse(file.ContentDisposition);

                // Some browsers send file names with full path.
                // We are only interested in the file name.
                var fileName = Path.GetFileName(fileContent.FileName.ToString().Trim('"'));
                var physicalPath = Path.Combine(HostingEnvironment.WebRootPath, "App_Data", fileName);

                // The files are not actually saved in this demo
                using (var fileStream = new FileStream(physicalPath, FileMode.Create))
                {
                    await file.CopyToAsync(fileStream);
                }
            }
        }

        // Return an empty string to signify success
        return Content("");
    }

	public ActionResult Remove(string[] fileNames)
	{
		// The parameter of the Remove action must be called "fileNames"

		if (fileNames != null)
		{
			foreach (var fullName in fileNames)
			{
				var fileName = Path.GetFileName(fullName);
				var physicalPath = Path.Combine(HostingEnvironment.WebRootPath, "App_Data", fileName);

				// TODO: Verify user permissions

				if (System.IO.File.Exists(physicalPath))
				{
					// The files are not actually removed in this demo
					// System.IO.File.Delete(physicalPath);
				}
			}
		}

		// Return an empty string to signify success
		return Content("");
	}
}
```

## Configuration

The following example demonstrates the basic configuration of the Upload HtmlHelper and how to get the Upload instance.

###### Example

```
@(Html.Kendo().Upload()
    .Name("files")
    .Async(a => a
        .Save("ChunkSave", "Upload")
        .Remove("Remove", "Upload")
        .AutoUpload(true)
        .ChunkSize(1100)
    )
    .DropZone(".dropZoneElement")
    .Validation(validation =>
    {
        validation.AllowedExtensions(new string[] { ".jpg", ".jpeg", ".png", ".bmp", ".gif" });
        validation.MinFileSize(500);
    })
)

<script type="text/javascript">
$(function() {
    //Notice that the Name() of the Upload is used to get its client-side instance.
    var files = $("#files").data("kendoUpload");
});
</script>
```

## See Also

* [JavaScript API Reference of the Upload](http://docs.telerik.com/kendo-ui/api/javascript/ui/upload)
* [Upload HtmlHelper for ASP.NET MVC](http://docs.telerik.com/aspnet-mvc/helpers/upload/overview)
* [Upload Official Demos](http://demos.telerik.com/aspnet-core/upload/index)
* [Overview of Telerik UI for ASP.NET Core]({% slug overview_aspnetmvc6_aspnetmvc %})
* [Get Started with Telerik UI for ASP.NET Core in ASP.NET Core Projects]({% slug gettingstarted_aspnetmvc6_aspnetmvc %})
* [Get Started with Telerik UI for ASP.NET Core in ASP.NET Core Projects with the CLI]({% slug gettingstartedcli_aspnetmvc6_aspnetmvc %})
* [Known Issues with Telerik UI for ASP.NET Core]({% slug knownissues_aspnetmvc6_aspnetmvc %})
