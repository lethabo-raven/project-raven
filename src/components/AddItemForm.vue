<template>
  <div>
  <div class="container text-left">
    <div class="row align-items-start">
      <div class="col">
      <div class="form-item">
          <datepicker :style="{ width: '300px' }"
                  :default-value="defaultValue"
                  :format="'yyyy-MM-dd'"
                  :name="Date"
                  :label="'Date:'"
                  :value="date"
              ></datepicker>
        </div>
  <div class="form-item">
    <dropdownlist
      :style="{ width: '300px' }"
      :data-items="items"
      :text-field="'Court'"
      :label="'Court:'"
      :name="'Court'"
      @change="courtChange"
      @filterchange="filterChangeCourt"
      :default-item="defaultCourt"
      :value="court"
      :filterable="true"
    />
  </div>
  <div class="form-item" style="display: none;">
    <dropdownlist
      :style="{ width: '300px' }"
      :label="'By Attorney/Candidate:'"
      :default-value="'Select Attorney/Candidate'"
      :data-items="datavalues"
    />
  </div>
</div>
<div class="col">
  <div class="form-item">
    <dropdownlist
      :label="'Category:'"
      :style="{ width: '300px' }"
      :disabled="!hasCourt"
      :data-items="products"
      :name="'Category'"
      :text-field="'Category'"
      @change="categoryChange"
      :default-item="defaultCategory"
      :value="Category"
    />
  </div>
  <div class="form-item">
    <dropdownlist
      :label="'Item:'"
      :style="{ width: '300px' }"
      :disabled="!hasCategory"
      :text-Field="'Item'"
      :data-items="orders"
      :filterable="true"
      :name="'Item'"
      @filterchange="filterChange"
      @change="itemChange"
      :default-item="defaultItem"
      :value="item"
      :loading="loading"
    />
  </div>
      <div class="form-item">
          <numerictextbox
            :style="{ width: '300px' }"
            :name="'Quantity'"
            :value="quantity"
            :label="'Quantity:'"
            :min="1"
            :defaultValue="defaultQuantity"
            v-on:change="quantityChange"
          ></numerictextbox>
      </div>
  </div>
  </div>
  </div>
  <div style="margin: 10px;">
    <kbutton :theme-color="'primary'" @click="save" :svg-icon="saveIcon"> Save </kbutton>
  </div>
  </div>
</template>

<script>
import { Dialog, DialogActionsBar } from '@progress/kendo-vue-dialogs';
import { NumericTextBox, Input, Checkbox } from '@progress/kendo-vue-inputs';
import { Label } from '@progress/kendo-vue-labels';
import { Button } from '@progress/kendo-vue-buttons';
import { cancelIcon, saveIcon } from '@progress/kendo-svg-icons';
import { DropDownList } from '@progress/kendo-vue-dropdowns';
import { DatePicker } from '@progress/kendo-vue-dateinputs';
import { data } from '@/appdata/data.js';
import { items } from '@/appdata/InvoiceItems.js';
import { filterBy } from '@progress/kendo-data-query';
import '@progress/kendo-theme-default/dist/all.css';

const delay = 500;
let quantityValue = 1;
const list = [];
const courts = [
        {Court:"CCMA"},
        {Court:"Competiton Tribunal"},
        {Court:"Competition Appeal"},
        {Court:"Constitutional Court Civil"},
        {Court:"Constitutional Court Criminal"},
        {Court:"Equality"},
        {Court:"Labour Court"},
        {Court:"Land Claims Court"},
        {Court:"Magistrate Court-Regional Criminal"},
        {Court:"High Court Civil"},
        {Court:"High Court Criminal"},
        {Court:"Supreme Court of Appeal Civil"},
        {Court:"Supreme Court of Appeal Criminal"},
        {Court:"Tax Court"},
        {Court:"Magistrate Court-District Criminal"},
        {Court:"Magistrate Court-Scale A"},
        {Court:"Magistrate Court-Scale B"},
        {Court:"Magistrate Court-Scale C"},
        {Court:"Magistrate Court-Scale D"}
      ];
export default {
  components: {
    'k-input': Input,
    numerictextbox: NumericTextBox,
    'k-dialog': Dialog,
    'dialog-actions-bar': DialogActionsBar,
    kbutton: Button,
    checkbox: Checkbox,
    klabel: Label,
    dropdownlist: DropDownList,
    'datepicker': DatePicker
  },
  props: {
    dataItem: Object,
  },
  data: function () {
    return {
      cancelIcon,
      saveIcon,
      itemData  : {
        ID: 1,
        Date: new Date(),
        Price: 0,
        Court: '',
        Category: '',
        Item: '',
        Quantity: 0,
        Amount: 0,
      },
      invitem: items,
      court: null,
      Category: null,
      item: null,
      items: courts,
      courtlist: this.courts,
      datavalues:[
        "Candidate","Attorney"
      ],
      defaultCourt: { Court: 'Select Court ...' },
      defaultCategory: { Category: 'Select Category ...' },
      defaultItem: { Item: 'Select Item ...' },
      orders: [],
      products: [],
      defaultValue: new Date(),
      defaultQuantity: 1,
      loading: false
    };
  },
  computed: {
    hasCourt: function () {
      return this.court && this.court !== this.defaultCourt
    },
    hasCategory: function () {
      return this.Category && this.Category !== this.defaultCategory;
    },
  },
  methods: {
    save() {
      const data1 = {
        ID: 1,
        Date: new Date(),
        Price: 0,
        Court: '',
        Category: '',
        Item: '',
        Quantity: 1,
        Amount: 0,
      };
      data1.Item = this.item.Item;
      data1.Court = this.item.Court;
      data1.Category = this.item.Category;
      data1.Price = this.item.Price;
      data1.Quantity = quantityValue;
      data1.Amount = this.item.Price * this.quantity;
      data1.ID = items.length + 1;
      this.invitem.push(data1);
      console.log(quantityValue);
    },
    dateChange(event) {
    const date = event.value;
    data1.Date = date;
  },
  quantityChange(event){
    quantityValue = 1;
    this.quantity = event.value;
    quantityValue = event.value;
  },
    courtChange(event) {
    const court1 = event.value;
    const products = data.filter(
      (Court) => Court.Court === court1.Court
    );
    this.court = court1;
    this.products = products;
    this.Category = null;
    this.item = null;
    },
    categoryChange(event) {
      const category1 = event.value;
      const orders = data.filter(
        (Category) => Category.Category === category1.Category && Category.Court === category1.Court
      );
      this.Category = category1;
      this.orders = orders;
      this.item = null;
      list.push(orders);
    },
    itemChange(event) {
      this.item = event.value;
    },
    filterChange(event) {
      clearTimeout(this.timeout);
      this.timeout = setTimeout(() => {
        this.orders = this.filterData(event.filter);
        this.loading = false;
      }, delay);

      this.loading = true;
    },
        filterData(filter) {
          const orders1 = list[list.length - 1];
            const data = orders1.slice();
            return filterBy(data, filter);
        },
    filterChangeCourt(event) {
      clearTimeout(this.timeout);
      this.timeout = setTimeout(() => {
        this.items = this.filterCourtData(event.filter);
        this.loading = false;
        
      }, delay);
      this.loading = true;
    },
    filterCourtData(filter) {
      const data = courts.slice();
      return filterBy(data, filter);
    },
  },
};
</script>
<style scoped>
.form-item{
  padding: 5px;
}
</style>