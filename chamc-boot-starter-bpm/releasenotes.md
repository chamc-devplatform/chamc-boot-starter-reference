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
		<td>1.2.0.RELEASE</td>
		<td></td>
		<td>
			1.添加获取可回退节点接口；<br />
			2.修改获取流程图方法：<a href="">点击查看</a>
		</td>
	</tr>
</table>
