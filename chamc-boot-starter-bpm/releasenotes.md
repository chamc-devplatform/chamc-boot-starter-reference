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
			1.添加邮件节点：<a href="https://chamc-devplatform.gitbook.io/chamc-boot-starter-reference/chamc-boot-starter-bpm/1.1.0.release/gong-neng-xiang-shu/4.2information-disign-1.1.0.release#you-jian-jie-dian-pei-zhi">点击查看</a><br />
			2.添加委派任务：<a href="https://chamc-devplatform.gitbook.io/chamc-boot-starter-reference/chamc-boot-starter-bpm/1.1.0.release/gong-neng-xiang-shu/4.4information-handover-1.1.0.release">点击查看</a><br />
			3.添加抄送配置：<a href="https://chamc-devplatform.gitbook.io/chamc-boot-starter-reference/chamc-boot-starter-bpm/1.1.0.release/gong-neng-xiang-shu/4.2information-disign-1.1.0.release#chao-song-pei-zhi">点击查看</a><br />
			4.添加查询租户下所有流程接口：<a href="https://chamc-devplatform.gitbook.io/chamc-boot-starter-reference/chamc-boot-starter-bpm/1.1.0.release/6.interface-1.1.0.release#irepositoryservice">点击查看</a><br />
			5.添加查询已结束流程实例接口：<a href="https://chamc-devplatform.gitbook.io/chamc-boot-starter-reference/chamc-boot-starter-bpm/1.1.0.release/6.interface-1.1.0.release#iinstanceservice">点击查看</a><br />
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
		<td></td>
		<td>
			1.添加获取可回退节点接口；<a href="https://chamc-devplatform.gitbook.io/chamc-boot-starter-reference/chamc-boot-starter-bpm/1.2.0.release/6.interface-1.2.0.release#6-cha-xun-ke-hui-tui-jie-dian">点击查看</a><br />
			2.修改获取流程图方法【同时原版流程图以及新版流程图均已添加候选用户的显示】：<a href="https://chamc-devplatform.gitbook.io/chamc-boot-starter-reference/chamc-boot-starter-bpm/1.2.0.release/gong-neng-xiang-shu/4.3information-diagram-1.2.0.release">点击查看</a> <br />
			3.添加解析首节点处理人接口；<a href="https://chamc-devplatform.gitbook.io/chamc-boot-starter-reference/chamc-boot-starter-bpm/1.2.0.release/6.interface-1.2.0.release#2-cha-xun-jie-dian-chu-li-ren">点击查看</a><br />
			4.添加解析指定节点处理人接口；<a href="https://chamc-devplatform.gitbook.io/chamc-boot-starter-reference/chamc-boot-starter-bpm/1.2.0.release/6.interface-1.2.0.release#2-cha-xun-jie-dian-chu-li-ren">点击查看</a><br />
			5.流程支持流程分类的管理：<a href="https://chamc-devplatform.gitbook.io/chamc-boot-starter-reference/chamc-boot-starter-bpm/1.2.0.release/gong-neng-xiang-shu/4.1information-platform-1.2.0.release#liu-cheng-fen-lei-guan-li">点击查看</a><br />
			6.添加获取租户下所有流程分类接口：<a href="https://chamc-devplatform.gitbook.io/chamc-boot-starter-reference/chamc-boot-starter-bpm/1.2.0.release/6.interface-1.2.0.release#irepositoryservice">点击查看</a> <br />
			7.待办已办支持根据流程分类查询；<a href="https://chamc-devplatform.gitbook.io/chamc-boot-starter-reference/chamc-boot-starter-bpm/1.2.0.release/6.interface-1.2.0.release#1-cha-xun-dai-ban">待办接口查看；</a>&nbsp;&nbsp;<a href="https://chamc-devplatform.gitbook.io/chamc-boot-starter-reference/chamc-boot-starter-bpm/1.2.0.release/6.interface-1.2.0.release#2-cha-xun-yi-ban">已办接口查看</a>
		</td>
	</tr>
</table>
