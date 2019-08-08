# 版本变更历史

<table>
	<tr>
	      <td>版本</td>
	      <td>发布时间</td>
	      <td>更新内容描述</td>
	</tr>
	<tr>
	      <td>1.0.0.RELEASE</td>
	      <td>2019-01-15</td>
	      <td>正式发版</td>
	</tr>
	<tr>
	      <td>1.0.1.RELEASE</td>
	      <td>2019-01-24</td>
	      <td>
	         1.修复发起流程指定下一节点机构无效的缺陷，任务号：#21538<br />
	         2.修复条件分支表达式复杂导致完成任务失败的问题，任务号：#21629
	      </td>
	</tr>
	<tr>
		<td>1.1.0.RELEASE</td>
		<td>2019-03-21</td>
		<td>
			1.添加邮件节点：<a href="./1.3.0.RELEASE/4.2information-disign-1.3.0.RELEASE.md#邮件节点配置">点击查看</a><br />
			2.添加委派任务：<a href="./1.3.0.RELEASE/4.4information-handover-1.3.0.RELEASE.md">点击查看</a><br />
			3.添加抄送配置：<a href="./1.3.0.RELEASE/4.2information-disign-1.3.0.RELEASE.md#抄送配置">点击查看</a><br />
			4.添加查询租户下所有流程接口：<a href="./1.3.0.RELEASE/6.interface-1.3.0.RELEASE.md#irepositoryservice">点击查看</a><br />
			5.添加查询已结束流程实例接口：<a href="./1.3.0.RELEASE/6.interface-1.3.0.RELEASE.md#流程实例查询">点击查看</a><br />
			6.bpm-api部分POST接口做幂等处理，限制一分钟内不允许发起相同参数流程。<br />	
			7.修复缺陷。sys_access_log表添加blob型字段字段params_detail_和result_detail，任务号：#22539<br />	
			8.修复缺陷。节点配置选中机构加角色，但未查找到用户，任务号：#21914 <br />	
		</td>
	</tr>
	<tr>
		<td>1.1.1.RELEASE</td>
		<td>2019-04-12</td>
		<td>
			1.修复缺陷。工作流使用queryNextTaskConfig接口查询时未使用先遣节点传入的流程变量，任务号：#23433<br />
			2.修复缺陷。bactToReject 的流程节点，查不到已办，任务号：#23251<br />
			3.修复缺陷。queryNextTaskInfo报错请传入分支变量，查询单一节点的处理人信息，任务号：#22980<br />
			4.修复缺陷。工作流-组织数据同步失败时，无法选择租户，任务号：#23489<br />
			5.修复缺陷。工作流多次流转后，流程图示出现较长的空白区域，任务号：#21722<br />
			6.修复缺陷。配置催办后再清除，页面提示不正确，任务号：#21896<br />	
		</td>
	</tr>
	<tr>
		<td>1.2.0.RELEASE</td>
		<td>2019-05-10</td>
		<td>
			1.添加获取可回退节点接口；<a href="./1.2.0.RELEASE/6.interface-1.2.0.RELEASE.md#6查询可回退节点">点击查看</a><br />
			2.修改获取流程图方法【同时原版流程图以及新版流程图均已添加候选用户的显示】：<a href="./1.2.0.RELEASE/4.3information-diagram-1.2.0.RELEASE.md">点击查看</a> <br />
			3.添加解析首节点处理人接口；<a href="./1.2.0.RELEASE/6.interface-1.2.0.RELEASE.md#2查询节点处理人">点击查看</a><br />
			4.添加解析指定节点处理人接口；<a href="./1.2.0.RELEASE/6.interface-1.2.0.RELEASE.md#2查询节点处理人">点击查看</a><br />
			5.流程支持流程分类的管理：<a href="./1.2.0.RELEASE/4.1information-platform-1.2.0.RELEASE.md#流程分类管理">点击查看</a><br />
			6.添加获取租户下所有流程分类接口：<a href="./1.2.0.RELEASE/6.interface-1.2.0.RELEASE.md#irepositoryservice">点击查看</a> <br />
			7.待办已办支持根据流程分类查询；<a href="./1.2.0.RELEASE/6.interface-1.2.0.RELEASE.md#1查询待办">待办接口查看；</a>&nbsp;&nbsp;<a href="./1.2.0.RELEASE/6.interface-1.2.0.RELEASE.md#2查询已办">已办接口查看</a> <br />
            8.支持待办连岗审批；<a href="./1.2.0.RELEASE/4.2information-disign-1.2.0.RELEASE.md#是否连岗审批">连岗审批配置</a><br />
			9.支持角色会签：<a href="./1.2.0.RELEASE/4.5.1.2.0.RELEASE-new-1.2.0.RELEASE.md#支持角色会签">点击查看</a><br />
			10.变量支持object类型：<a href="./1.2.0.RELEASE/4.5.1.2.0.RELEASE-new-1.2.0.RELEASE.md#变量支持object类型">点击查看</a><br />
			11.查询节点配置信息添加催办配置字段：<a href="./1.2.0.RELEASE/4.5.1.2.0.RELEASE-new-1.2.0.RELEASE.md#查询节点配置信息添加催办配置字段">点击查看</a><br />
			12.会签节点支持回退，以及支持从会签节点取回待办：<a href="./1.2.0.RELEASE/4.5.1.2.0.RELEASE-new-1.2.0.RELEASE.md#会签节点支持回退以及支持从会签节点取回待办">点击查看</a><br />
			13.按钮增加是否可用标识：<a href="./1.2.0.RELEASE/4.5.1.2.0.RELEASE-new-1.2.0.RELEASE.md#按钮增加是否可用标识">点击查看</a><br />
		</td>
	</tr>
       <tr>
		<td>1.2.1.RELEASE</td>
		<td>2019-06-18</td>
		<td>
			1.修复缺陷。项目有根路径导致流程图无法查看的缺陷，任务号：#26295<br />
		</td>
	</tr>
	<tr>
		<td>1.3.0.RELEASE</td>
		<td></td>
		<td>
		1.增加流程挂起激活接口；<a href="./1.3.0.RELEASE/6.interface-1.3.0.RELEASE.md#流程操作">点击查看</a><br />
		2.增加流程变量设置、移除、查询接口；<a href="./1.3.0.RELEASE/6.interface-1.3.0.RELEASE.md#流程实例与任务变量">流程实例变量与任务变量关系</a>； <a href="./1.3.0.RELEASE/6.interface-1.3.0.RELEASE.md#流程实例变量">通过流程实例操作变量</a>； <a href="./1.3.0.RELEASE/6.interface-1.3.0.RELEASE.md#7通过任务操作变量">通过任务操作变量</a><br />
		3.流程实例信息增加流程状态；<a href="./1.3.0.RELEASE/8.model-1.3.0.RELEASE.md#processinstance">点击查看</a><br />
        4.增加复合参数查询委托接口；<a href="./1.3.0.RELEASE/6.interface-1.3.0.RELEASE.md#ihandoverservice">点击查看</a><br />
		5.支持子流程及调用活动；<a href="./1.3.0.RELEASE/4.2information-disign-1.3.0.RELEASE.md#子流程配置">子流程配置</a> ；<a href="./1.3.0.RELEASE/4.2information-disign-1.3.0.RELEASE.md#调用活动配置">调用活动配置</a>；<a href="./1.3.0.RELEASE/8.model-1.3.0.RELEASE.md#activityconfig节点配置">节点配置模型（增加父配置）</a><br />
		6.修复缺陷：使用工作流queryTransferDetail查询流转明细，返回的list的顺序有点小问题，任务号：#27311   <br />
		7.修复缺陷：【工作流平台】—— 任务转办多次至同一个人时，已办查询接口报错，任务号：#27194  <br />
		8.修复缺陷：工作流通过后端代码启动时，无法通过setNextOrgId传参无法完成机构+角色的流转，任务号：27015 <br />
		9.修复缺陷：客户风险限额一期-关于流程流转中变量重复问题优化，任务号：24440 <br />
		10.流程操作接口、任务操作接口代码重构
		</td>
	</tr>
</table>
