<template>
	<cl-select-table
		v-model="value"
		:title="t('选择商品')"
		:service="service.order.goods"
		:columns="columns"
		:multiple="multiple"
		:dict='{"img":"pic","text":"name"}'
		pickerType="default"
	/>
</template>

<script setup lang="ts">
	defineOptions({
		name: "order-goods-select"
	});

	import { useCool } from "/@/cool";
	import { useI18n } from "vue-i18n";
	import { CrudProps } from "/#/crud";
	import { reactive, ref, useModel } from "vue";

	const props = defineProps({
		...CrudProps,
		modelValue: null,
		multiple: Boolean
	});

	const { service } = useCool();
	const { t } = useI18n();

	const value = useModel(props, "modelValue");

	// // 选项
	// const options = reactive({
	// 	isDelete: [
	// 		{ label: t("正常"), value: 0, type: "danger" },
	// 		{ label: t("已删除"), value: 1, type: "success" }
	// 	]
	// });

	const columns = ref([
		{ label: t("名称"), prop: "categoryName", minWidth: 140 },
		{
			label: t("图片"),
			prop: "pic",
			minWidth: 100,
			component: { name: "cl-image", props: { size: 60 } }
		},
		{ label: t("名称"), prop: "name", minWidth: 140 },
		{ label: t("SKU"), prop: "sku", minWidth: 140 },
		{ label: t("价格"), prop: "price", minWidth: 140, sortable: "custom" },
		{ label: t("库存"), prop: "stock", minWidth: 140, sortable: "custom" },
		{ label: t("排序"), prop: "sort", minWidth: 140, sortable: "custom" }
	]);
</script>
