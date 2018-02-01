

```java
//前端控制器分派方法
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
	HttpServletRequest processedRequest = request;
	HandlerExecutionChain mappedHandler = null;
	int interceptorIndex = -1;

	try {
		ModelAndView mv;
		boolean errorView = false;

		try {
			//检查是否是请求是否是multipart（如文件上传），如果是将通过MultipartResolver解析
			processedRequest = checkMultipart(request);
			//步骤2、请求到处理器（页面控制器）的映射，通过HandlerMapping进行映射
			mappedHandler = getHandler(processedRequest, false);
			if (mappedHandler == null || mappedHandler.getHandler() == null) {
				noHandlerFound(processedRequest, response);
				return;
			}
			//步骤3、处理器适配，即将我们的处理器包装成相应的适配器（从而支持多种类型的处理器）
			HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());

			// 304 Not Modified缓存支持
			//此处省略具体代码

			// 执行处理器相关的拦截器的预处理（HandlerInterceptor.preHandle）
			//此处省略具体代码

			// 步骤4、由适配器执行处理器（调用处理器相应功能处理方法）
			mv = ha.handle(processedRequest, response, mappedHandler.getHandler());

			// Do we need view name translation?
			if (mv != null && !mv.hasView()) {
				mv.setViewName(getDefaultViewName(request));
			}

			// 执行处理器相关的拦截器的后处理（HandlerInterceptor.postHandle）
			//此处省略具体代码
		}
		catch (ModelAndViewDefiningException ex) {
			logger.debug("ModelAndViewDefiningException encountered", ex);
			mv = ex.getModelAndView();
		}
		catch (Exception ex) {
			Object handler = (mappedHandler != null ? mappedHandler.getHandler() : null);
			mv = processHandlerException(processedRequest, response, handler, ex);
			errorView = (mv != null);
		}

		//步骤5 步骤6、解析视图并进行视图的渲染
		//步骤5 由ViewResolver解析View（viewResolver.resolveViewName(viewName, locale)）
		//步骤6 视图在渲染时会把Model传入（view.render(mv.getModelInternal(), request, response);）
		if (mv != null && !mv.wasCleared()) {
			render(mv, processedRequest, response);
			if (errorView) {
				WebUtils.clearErrorRequestAttributes(request);
			}
		}
		else {
			if (logger.isDebugEnabled()) {
				logger.debug("Null ModelAndView returned to DispatcherServlet with name '" + getServletName() +
						"': assuming HandlerAdapter completed request handling");
			}
		}

		// 执行处理器相关的拦截器的完成后处理（HandlerInterceptor.afterCompletion）
		//此处省略具体代码

		catch (Exception ex) {
			// Trigger after-completion for thrown exception.
			triggerAfterCompletion(mappedHandler, interceptorIndex, processedRequest, response, ex);
			throw ex;
		}
		catch (Error err) {
			ServletException ex = new NestedServletException("Handler processing failed", err);
			// Trigger after-completion for thrown exception.
			triggerAfterCompletion(mappedHandler, interceptorIndex, processedRequest, response, ex);
			throw ex;
		}

	finally {
		// Clean up any resources used by a multipart request.
		if (processedRequest != request) {
		cleanupMultipart(processedRequest);
		}
	}
}
```
