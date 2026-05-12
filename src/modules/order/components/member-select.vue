<template>
	<cl-select-table
		v-model="value"
		:title="t('选择会员管理')"
		:service="service.order.member"
		:columns="columns"
		:multiple="multiple"
		:dict='{"text":"name"}'
		pickerType="text"
	/>
</template>

<script setup lang="ts">
	defineOptions({
		name: "order-member-select"
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

	// 选项
	const options = reactive({
		gender: [
			{ label: t("保密"), value: 0 },
			{ label: t("男"), value: 1 },
			{ label: t("女"), value: 2 }
		],
		status: [
			{ label: t("正常"), value: 0, type: "danger" },
			{ label: t("禁用"), value: 1, type: "success" }
		],
		isDelete: [
			{ label: t("正常"), value: 0, type: "danger" },
			{ label: t("已删除"), value: 1, type: "success" }
		]
	});

	const columns = ref([
		{
			label: t("头像"),
			prop: "avatar",
			minWidth: 100,
			component: { name: "cl-image", props: { size: 60 } }
		},
		{ label: t("昵称"), prop: "nickName", minWidth: 140 },
		{ label: t("姓名"), prop: "name", minWidth: 140 },
		{ label: t("性别"), prop: "gender", minWidth: 120, dict: options.gender },
		{ label: t("地址"), prop: "address", minWidth: 120 },
		{ label: t("电话"), prop: "phone", minWidth: 140 },
		{ label: t("邮箱"), prop: "email", minWidth: 140 },
		{ label: t("状态"), prop: "status", minWidth: 120, dict: options.status },
		{
			label: t("删除标记"),
			prop: "isDelete",
			minWidth: 100,
			component: { name: "cl-switch" },
			dict: options.isDelete
		}
	]);
</script>
