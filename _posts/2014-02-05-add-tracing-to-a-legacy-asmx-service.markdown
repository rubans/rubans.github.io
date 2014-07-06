---
layout: post
title: "Add tracing to a legacy ASMX service"
description: ""
category: 
tags: []
---
To add tracing to legacy ASMX service for detailed trouble shooting, add the following to the web.config:

    <system.diagnostics>
	    <trace autoflush="true" />
	    <sources>
	    <source name="System.Web.Services.Asmx">
	    <listeners>
		      <add type="System.Diagnostics.DefaultTraceListener" name="Default">
		    	<filter type="" />
		      </add>
		      <add initializeData="local.log" type="System.Diagnostics.TextWriterTraceListener"
		    name="AsmxTraceFile" traceOutputOptions="LogicalOperationStack, DateTime, Timestamp, ProcessId, ThreadId">
		    	<filter type="" />
		      </add>
	    </listeners>
	      </source>
	    
	    </sources>
	    <switches>
	    	<add name="System.Web.Services.Asmx" value="Verbose"  />
	    </switches>
    </system.diagnostics>
    