<template>
  <div>
    <b-container class="bv-example-row">
      <b-row>
        <!-- <b-col v-for="item in items" :key="item.name" class="unit_class">{{ item.name }}</b-col> -->
        <b-col sm="7">
          <b-button v-on:click="add_item(item.name)" v-for="item in items" :key="item.name" class="unit_class center_element" variant="outline-info" v-bind:value="item.name">{{ item.name }} (&#8377;{{ item.price }})</b-button>
        </b-col>
        <b-col sm="5" class="font_style p-3" style="border: 2px solid grey">
          <table class="table">
            <thead>
              <tr>
                <th scope="col">ITEM</th>
                <th scope="col">PRICE</th>
                <th scope="col">QTY</th>
                <th scope="col">AMOUNT</th>
                <th scope="col">ACTION</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in selected_items" :key="item.name">
                <td>{{ item.name }}</td>
                <td>&#8377;{{ item.price }}</td>
                <td><input @change="on_change_qty(item.name, $event.target.value)" type="number" id="qty" min="1" name="qty" :value="item.quantity"></td>
                <td>&#8377;{{ item.amount }}</td>
                <td><b-link @click="delete_item(item.name)"><b-icon icon="x-circle" scale="2" variant="danger"></b-icon></b-link></td>
              </tr> 
              <tr v-if="!selected_items.length">
                <td :colspan="5" class="center_element">no items selected</td>
              </tr>
            </tbody>
          </table>
          <hr>
          <div class="float-right m-2">
          <h6>SUBTOTAL : <b-badge>&#8377; {{ subtotal }}</b-badge></h6>
          <label for="discount"><h6>DISCOUNT : </h6></label>&nbsp;
          <input v-on:change="on_change_discount()" v-model="discount" type="number" id="discount" min="0" name="discount" value="0">
          <h6>TOTAL : <b-badge>&#8377; {{ total }}</b-badge></h6><hr>
          </div>
          
          
          <b-button v-b-modal.modal-1 block variant="outline-info" v-on:click="reset()">CHECKOUT</b-button>
        </b-col>
      </b-row>
    </b-container>

    <b-modal id="modal-1" title="BootstrapVue">
      <p>TOTAL : {{ this.total }}</p>
      <p>RECEIVED : <input type="number" v-on:change="update_refund()" v-model="received_amount" id="received_amount" min="0" value="0"></p>
      <p>REFUND : {{ refund }}</p>
    </b-modal>
    
  </div>
</template>

<script>
export default {
  name: "HelloWorld",

  data() {
    return {
      items: [
        { name: "Roti", price: 5, status: 1 },
        { name: "Rice", price: 50, status: 1 },
        { name: "Parotha", price: 10, status: 1 },
        { name: "Kadhai Paneer", price: 150, status: 1 },
        { name: "Aalu Bhujya", price: 100, status: 1 },
        { name: "Biryani", price: 300, status: 1 },
        { name: "Chicken Curry", price: 200, status: 1 },
        { name: "Dhosa", price: 80, status: 1 },
        { name: "Cold Drink", price: 50, status: 1 },
        { name: "Water", price: 20, status: 1 },
        { name: "Gulab Jamun", price: 20, status: 1 },
        { name: "Pizza", price: 200, status: 1 },
        { name: "Burger", price: 250, status: 1 },
        { name: "Sandwich", price: 90, status: 1 },
        { name: "Egg Puffs", price: 20, status: 1 },
        { name: "Chocolate", price: 50, status: 1 },
        { name: "Kheer", price: 60, status: 1 },
        { name: "Halua", price: 100, status: 1 },
        { name: "Curd", price: 30, status: 1 },
        { name: "Pickles", price: 10, status: 1 },
      ],

      selected_items: [],
      subtotal : 0,
      discount : 0,
      total : 0,
      received_amount : 0,
      refund : 0

    };
  },

  methods: {

    get_price(name){
      let i;
      
      for(i in this.items){
        if(this.items[i].name == name){
          return this.items[i].price;
        }
      }
    },

    add_item_into_selected_items(name, price){

      let arr = {
        name : name,
        price : price,
        quantity : 1,
        amount : price * 1
      };

      this.selected_items.push(arr);
      // console.log(this.selected_items);
    },

    find_subtotal_total(){
      let i;
      let subtotal = 0;

      for(i in this.selected_items){
        subtotal += this.selected_items[i].amount;
      }

      this.subtotal = subtotal;

      this.total = this.subtotal - this.discount;
    },

    on_change_qty(name, qty){
      let i;

      console.log(name, qty);
      for(i in this.selected_items){
        if(this.selected_items[i].name == name){
          this.selected_items[i].quantity = qty;
          this.selected_items[i].amount = this.selected_items[i].price * qty;
          
          break;
        }
      }

      this.find_subtotal_total();

    },

    on_change_discount(){
      this.total = this.subtotal - this.discount;
    },

    update_refund(){
      this.refund = this.received_amount - this.total;
    },

    delete_item(name){

      this.selected_items = this.selected_items.filter(function(item) {
        return item.name != name
      })  

      this.find_subtotal_total();
      // console.log(name);
    },

    is_dublicate(name){
      let single_item = this.selected_items.filter(function(item){
        return item.name == name
      })

      if(single_item.length) return true;
      else return false;
      
    },

    add_item(name){

      if(this.is_dublicate(name)){
        let i;

        for(i in this.selected_items){
          if(this.selected_items[i].name == name){
            this.selected_items[i].quantity ++;
            this.on_change_qty(name, this.selected_items[i].quantity);
            break;
          }
        }

        // console.log(this.selected_items);
      }else{
        let price = this.get_price(name);
      
        this.add_item_into_selected_items(name, price);

        this.find_subtotal_total();
      }
      
    }
  }
};
</script>

<style scoped>
.bv-example-row {
  margin: 30px;
}

.unit_class {
  font-family: "Lucida Console", "Courier New", monospace;
  padding: 15px;
  margin: 5px;
  width: 110px;
  height: 110px;
}

.center_element {
  text-align: center;
}

.font_style {
  font-family: Courier;
}

.alert_manage {
  width: auto;
}

#discount, #qty, #received_amount {
  width: 50px;
  border-radius: 5px;
}
</style>