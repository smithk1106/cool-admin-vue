<template>
	<cl-crud ref="Crud">
		<cl-row>
			<!-- 刷新按钮 -->
			<cl-refresh-btn />
			<!-- 新增按钮 -->
			<cl-add-btn />
			<!-- 删除按钮 -->
			<cl-multi-delete-btn />
			<cl-flex1 />
			<!-- 条件搜索 -->
			<cl-search ref="Search" />
		</cl-row>

		<cl-row>
			<!-- 数据表格 -->
			<cl-table ref="Table" />
		</cl-row>

		<cl-row>
			<cl-flex1 />
			<!-- 分页控件 -->
			<cl-pagination />
		</cl-row>

		<!-- 新增、编辑 -->
		<cl-upsert ref="Upsert" />
	</cl-crud>
</template>

<script lang="ts" setup>
	defineOptions({
		name: "order-info"
	});

	import { useCrud, useTable, useUpsert, useSearch } from "@cool-vue/crud";
	import { useCool } from "/@/cool";
	import { useI18n } from "vue-i18n";
	import { reactive } from "vue";
	import GoodsSelect from "/$/order/components/goods-select.vue";
	import MemberSelect from "/$/order/components/member-select.vue";

	const { service } = useCool();
	const { t } = useI18n();

	// 选项
	const options = reactive({
		payType: [
			{ label: t('未知'), value: 0 },
			{ label: t('信用卡'), value: 1 },
			{ label: t('电子支付'), value: 2 },
			{ label: t('代引'), value: 3 },
			{ label: t('汇款'), value: 4 }
		],
		status: [
			{ label: t('待处理'), value: 0 },
			{ label: t('待汇款'), value: 1 },
			{ label: t('已发送'), value: 2 },
			{ label: t('返品中'), value: 3 },
			{ label: t('取消'), value: 4 }
		],
		isDelete: [
			{ label: t('未删除'), value: 0, type: "danger" },
			{ label: t('已删除'), value: 1, type: "success" }
		]
	});

	// cl-upsert
	const Upsert = useUpsert({
		items: [
			{ label: t('商品图'), prop: "pic", component: { name: "cl-upload" } },
			{
				label: t('商品名'),
				prop: "goodsName",
				component: { name: "el-input", props: { clearable: true } },
				span: 12,
				required: true
			},
			{
				label: t('姓名'),
				prop: "name",
				component: { name: "el-input", props: { clearable: true } },
				span: 12,
				required: true
			},
			{
				label: t('电话'),
				prop: "phone",
				component: { name: "el-input", props: { clearable: true } },
				span: 12,
				required: true
			},
			{
				label: t('地址'),
				prop: "address",
				component: { name: "el-input", props: { clearable: true } },
				span: 12,
				required: true
			},
			{
				label: t('邮箱'),
				prop: "email",
				component: { name: "el-input", props: { clearable: true } },
				span: 12
			},
			{
				label: t('支付方式'),
				prop: "payType",
				component: { name: "cl-select", props: { options: options.payType } },
				value: 0,
				span: 12,
				required: true
			},
			{
				label: t('状态'),
				prop: "status",
				component: { name: "cl-select", props: { options: options.status } },
				value: 0,
				span: 12,
				required: true
			},
			{
				label: t('备注'),
				prop: "remark",
				component: { name: "el-input", props: { type: "textarea", rows: 4 } }
			},
			{
				label: t('选择商品'),
				prop: "goodsId",
				component: { vm: GoodsSelect },
				span: 12,
				required: true
			},
			{
				label: t('选择会员'),
				prop: "memberId",
				component: { vm: MemberSelect },
				span: 12,
				required: true
			},
			{
				label: t('删除'),
				prop: "isDelete",
				component: { name: "el-radio-group", options: options.isDelete },
				value: 0,
				required: true
			}
		]
	});

	// cl-table
	const Table = useTable({
		columns: [
			{ type: "selection" },
            { label: t('商品名'), prop: "goodsName", minWidth: 140 },
			{ label: t('SKU'), prop: "goodsSku", minWidth: 140 },
			{
				label: t('商品图'),
				prop: "pic",
				minWidth: 100,
				component: { name: "cl-image", props: { size: 60 } }
			},
			{ label: t('姓名'), prop: "name", minWidth: 140 },
			{ label: t('电话'), prop: "phone", minWidth: 140 },
			{ label: t('地址'), prop: "address", minWidth: 120 },
			{ label: t('邮箱'), prop: "email", minWidth: 140 },
			{
				label: t('支付方式'),
				prop: "payType",
				minWidth: 120,
				dict: options.payType
			},
			{ label: t('状态'), prop: "status", minWidth: 120, dict: options.status },
			{ label: t('备注'), prop: "remark", showOverflowTooltip: true, minWidth: 200 },
			{
				label: t('删除标记'),
				prop: "isDelete",
				minWidth: 100,
				component: { name: "cl-switch" },
				dict: options.isDelete
			},
			{
				label: t('创建时间'),
				prop: "createTime",
				minWidth: 170,
				sortable: "desc",
				component: { name: "cl-date-text" }
			},
			{
				label: t('更新时间'),
				prop: "updateTime",
				minWidth: 170,
				sortable: "custom",
				component: { name: "cl-date-text" }
			},
			{ type: "op", buttons: ["edit", "delete"] }
		]
	});

	// cl-search
	const Search = useSearch();

	// cl-crud
	const Crud = useCrud(
		{
			service: service.order.info
		},
		(app) => {
			app.refresh();
		}
	);

	// 刷新
	function refresh(params?: any) {
		Crud.value?.refresh(params);
	}
</script>
