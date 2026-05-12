<template>
	<cl-crud ref="Crud">
		<cl-row>
			<!-- 刷新按钮 -->
			<cl-refresh-btn />
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
	</cl-crud>
</template>

<script lang="ts" setup>
	defineOptions({
		name: "goods-ranking"
	});

	import { useCrud, useTable, useUpsert, useSearch } from "@cool-vue/crud";
	import { useCool } from "/@/cool";
	import { useI18n } from "vue-i18n";
	import { reactive } from "vue";

	const { service } = useCool();
	const { t } = useI18n();

	// 选项
	const options = reactive({
		comparePrev: [
			{ label: t('准备'), value: 0, type: "danger" },
			{ label: t('订单已发'), value: 2, type: "success" }
		],
	});

	// cl-table
	const Table = useTable({
		columns: [
			{ type: "selection" },
			{ label: t('Rank'), prop: "rank", minWidth: 50 },
			{ label: t('名称'), prop: "goodsName", minWidth: 200, align: "left" },
			// { label: t('价格'), prop: "price", minWidth: 100, sortable: "custom" },
			{ label: t('人气度'), prop: "score", minWidth: 100, sortable: "custom" },
			{ label: t('前日比'), prop: "dailyDiff", minWidth: 100, sortable: "custom", formatter(row, column, value, index) {
				return value > 10 ? "↑ "+value : "↓ "+value
			}, },
			{ label: t('推荐订购数'), prop: "score", minWidth: 100, sortable: "custom", formatter(row, column, value, index) {
				return value > 70 ? 20 : (value > 50 ? 10 : 0)
			} },
			// {
			// 	label: t('创建时间'),
			// 	prop: "createTime",
			// 	minWidth: 170,
			// 	sortable: "desc",
			// 	component: { name: "cl-date-text" }
			// },
			// {
			// 	label: t('更新时间'),
			// 	prop: "updateTime",
			// 	minWidth: 170,
			// 	sortable: "custom",
			// 	component: { name: "cl-date-text" }
			// }
		]
	});

	// cl-search
	const Search = useSearch();

	// cl-crud
	const Crud = useCrud(
		{
			service: service.marketing.goods_history
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
