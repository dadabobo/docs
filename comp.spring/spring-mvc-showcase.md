```
.
java/org/springframework
    mvc/extensions/ajax/
        AjaxUtils.java
    samples/mvc
        async
        convert
        data
        exceptions
        fileupload
        form
        mapping
        messageconverters
        redirect
        response
        simple
        validation
        views
resources
    log4j.xml
webapp
    resources
        form.css
        jquery/1.6/jquery.js
        jqueryform/2.8/jquery.form.js
        jqueryui/1.8
            jquery.ui.core.js
            jquery.ui.tabs.js
            jquery.ui.widget.js
            themes/base/
                images
                jquery.ui.all.css
                jquery.ui.base.css
                jquery.ui.core.css
                jquery.ui.tabs.css
                jquery.ui.theme.css
        json2.js
        messages
            error.png
            info.png
            messages.css
            success.png
            warning.png
    WEB-INF
        spring
            appServlet
                controllers.xml
                servlet-context.xml
            root-context.xml
        views
            fileupload.jsp
            form.jsp
            home.jsp
            redirect
                redirectResults.jsp
            views
                dataBinding.jsp
                html.jsp
                viewName.jsp
        web.xml
```

---

.
java/org/springframework
    mvc/extensions/ajax/
        AjaxUtils.java
    samples/mvc
        async
            CallableController.java
            DeferredResultController.java
            JavaBean.java
            TimeoutCallableProcessingInterceptor.java
        convert
            ConvertController.java
            JavaBean.java
            MaskFormatAnnotationFormatterFactory.java
            MaskFormat.java
            NestedBean.java
            SocialSecurityNumber.java
        data
            custom
                CustomArgumentController.java
                CustomArgumentResolver.java
                RequestAttribute.java
            JavaBean.java
            RequestDataController.java
            standard
                StandardArgumentsController.java
        exceptions
            BusinessException.java
            ExceptionController.java
            GlobalExceptionHandler.java
        fileupload
            FileUploadController.java
        form
            FormBean.java
            FormController.java
            InquiryType.java
        mapping
            ClasslevelMappingController.java
            JavaBean.java
            MappingController.java
        messageconverters
            JavaBean.java
            MessageConvertersController.java
        redirect
            RedirectController.java
        response
            ResponseController.java
        simple
            SimpleController.java
            SimpleControllerRevisited.java
        validation
            JavaBean.java
            ValidationController.java
        views
            JavaBean.java
            ViewsController.java
resources
    log4j.xml
webapp
    resources
        form.css
        jquery
            1.6
                jquery.js
        jqueryform
            2.8
                jquery.form.js
        jqueryui/1.8
            jquery.ui.core.js
            jquery.ui.tabs.js
            jquery.ui.widget.js
            themes/base/
                images
                    ui-bg_flat_0_aaaaaa_40x100.png
                    ui-bg_flat_75_ffffff_40x100.png
                    ui-bg_glass_55_fbf9ee_1x400.png
                    ui-bg_glass_65_ffffff_1x400.png
                    ui-bg_glass_75_dadada_1x400.png
                    ui-bg_glass_75_e6e6e6_1x400.png
                    ui-bg_glass_95_fef1ec_1x400.png
                    ui-bg_highlight-soft_75_cccccc_1x100.png
                    ui-icons_222222_256x240.png
                    ui-icons_2e83ff_256x240.png
                    ui-icons_454545_256x240.png
                    ui-icons_888888_256x240.png
                    ui-icons_cd0a0a_256x240.png
                jquery.ui.all.css
                jquery.ui.base.css
                jquery.ui.core.css
                jquery.ui.tabs.css
                jquery.ui.theme.css
        json2.js
        messages
            error.png
            info.png
            messages.css
            success.png
            warning.png
    WEB-INF
        spring
            appServlet
                controllers.xml
                servlet-context.xml
            root-context.xml
        views
            fileupload.jsp
            form.jsp
            home.jsp
            redirect
                redirectResults.jsp
            views
                dataBinding.jsp
                html.jsp
                viewName.jsp
        web.xml


---
.
├── java
│   └── org
│       └── springframework
│           ├── mvc
│           │   └── extensions
│           │       └── ajax
│           │           └── AjaxUtils.java
│           └── samples
│               └── mvc
│                   ├── async
│                   │   ├── CallableController.java
│                   │   ├── DeferredResultController.java
│                   │   ├── JavaBean.java
│                   │   └── TimeoutCallableProcessingInterceptor.java
│                   ├── convert
│                   │   ├── ConvertController.java
│                   │   ├── JavaBean.java
│                   │   ├── MaskFormatAnnotationFormatterFactory.java
│                   │   ├── MaskFormat.java
│                   │   ├── NestedBean.java
│                   │   └── SocialSecurityNumber.java
│                   ├── data
│                   │   ├── custom
│                   │   │   ├── CustomArgumentController.java
│                   │   │   ├── CustomArgumentResolver.java
│                   │   │   └── RequestAttribute.java
│                   │   ├── JavaBean.java
│                   │   ├── RequestDataController.java
│                   │   └── standard
│                   │       └── StandardArgumentsController.java
│                   ├── exceptions
│                   │   ├── BusinessException.java
│                   │   ├── ExceptionController.java
│                   │   └── GlobalExceptionHandler.java
│                   ├── fileupload
│                   │   └── FileUploadController.java
│                   ├── form
│                   │   ├── FormBean.java
│                   │   ├── FormController.java
│                   │   └── InquiryType.java
│                   ├── mapping
│                   │   ├── ClasslevelMappingController.java
│                   │   ├── JavaBean.java
│                   │   └── MappingController.java
│                   ├── messageconverters
│                   │   ├── JavaBean.java
│                   │   └── MessageConvertersController.java
│                   ├── redirect
│                   │   └── RedirectController.java
│                   ├── response
│                   │   └── ResponseController.java
│                   ├── simple
│                   │   ├── SimpleController.java
│                   │   └── SimpleControllerRevisited.java
│                   ├── validation
│                   │   ├── JavaBean.java
│                   │   └── ValidationController.java
│                   └── views
│                       ├── JavaBean.java
│                       └── ViewsController.java
├── resources
│   └── log4j.xml
└── webapp
    ├── resources
    │   ├── form.css
    │   ├── jquery
    │   │   └── 1.6
    │   │       └── jquery.js
    │   ├── jqueryform
    │   │   └── 2.8
    │   │       └── jquery.form.js
    │   ├── jqueryui
    │   │   └── 1.8
    │   │       ├── jquery.ui.core.js
    │   │       ├── jquery.ui.tabs.js
    │   │       ├── jquery.ui.widget.js
    │   │       └── themes
    │   │           └── base
    │   │               ├── images
    │   │               │   ├── ui-bg_flat_0_aaaaaa_40x100.png
    │   │               │   ├── ui-bg_flat_75_ffffff_40x100.png
    │   │               │   ├── ui-bg_glass_55_fbf9ee_1x400.png
    │   │               │   ├── ui-bg_glass_65_ffffff_1x400.png
    │   │               │   ├── ui-bg_glass_75_dadada_1x400.png
    │   │               │   ├── ui-bg_glass_75_e6e6e6_1x400.png
    │   │               │   ├── ui-bg_glass_95_fef1ec_1x400.png
    │   │               │   ├── ui-bg_highlight-soft_75_cccccc_1x100.png
    │   │               │   ├── ui-icons_222222_256x240.png
    │   │               │   ├── ui-icons_2e83ff_256x240.png
    │   │               │   ├── ui-icons_454545_256x240.png
    │   │               │   ├── ui-icons_888888_256x240.png
    │   │               │   └── ui-icons_cd0a0a_256x240.png
    │   │               ├── jquery.ui.all.css
    │   │               ├── jquery.ui.base.css
    │   │               ├── jquery.ui.core.css
    │   │               ├── jquery.ui.tabs.css
    │   │               └── jquery.ui.theme.css
    │   ├── json2.js
    │   └── messages
    │       ├── error.png
    │       ├── info.png
    │       ├── messages.css
    │       ├── success.png
    │       └── warning.png
    └── WEB-INF
        ├── spring
        │   ├── appServlet
        │   │   ├── controllers.xml
        │   │   └── servlet-context.xml
        │   └── root-context.xml
        ├── views
        │   ├── fileupload.jsp
        │   ├── form.jsp
        │   ├── home.jsp
        │   ├── redirect
        │   │   └── redirectResults.jsp
        │   └── views
        │       ├── dataBinding.jsp
        │       ├── html.jsp
        │       └── viewName.jsp
        └── web.xml

----
