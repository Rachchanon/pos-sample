<template>
  <div class="container">
    <!-- สำหรับ dev ไว้เช็คข้อมูล -->
    <!-- <div style="color:#fff">
      <h1 class="title">POS</h1>
      รายการสินค้าทั้งหมด* <b>allItem</b>
      <div>{{ allItem }}</div>

      <br />ข้อมูลสินค้าทั้งหมด ดึงจาก API* <b>allItemById</b>
      <div>{{ allItemById }}</div>

      <br />รายการราคาทั้งหมด* <b>allListPrice</b>
      <div>{{ allListPrice }}</div>

      <br />index รายการสินค้าทั้งหมด* <b>allItemIndex</b>
      <div>{{ allItemIndex }}</div>

      <br />รวมราคาสินค้าทั้งหมด* <b>sumPrice</b>
      <div>{{ sumPrice }}</div>

      <br />รายการที่มีส่วนลด* <b>listItem</b>
      <div>{{ listItem }}</div>

      <br />เช็คหาเซ็ทสินค้าที่ส่วนลด จากสินค้าทั้งหมด* <b>step2Filter</b>
      <div>{{ step2Filter }}</div>

      <br />ข้อมูลสินค้าที่ได้ส่วนลด ดึงจาก API* <b>step2FilterById</b>
      <div>{{ step2FilterById }}</div>

      <br />เช็คหาราคาสินค้าที่ส่วนลด จากสินค้าทั้งหมด <b>step2ListPrice</b>
      <div>{{ step2ListPrice }}</div>

      <br />index ของราคาที่มีส่วนลด ที่แยกออกมาที่ละเซ็ท* <b>step2ListIndex</b>
      <div>{{ step2ListIndex }}</div>

      <br />รวมส่วนลด* <b>sumDiscount</b>
      <div>{{ sumDiscount }}</div>

      <br />เซ็ทที่ได้ส่วนลดที่ยังเหลือ* <b>step3Filter</b>
      <div>{{ step3Filter }}</div>

      <br />ราคาของส่วนลดแต่ละเซ็ท* <b>listReduceDiscount</b>
      <div>{{ listReduceDiscount }}</div>

      <br />* <b>listReduceIndex</b>
      <div>{{ listReduceIndex }}</div>

      <br />* <b>reduceById</b>
      <div>{{ reduceById }}</div>

      <br />รวมเฉพาะ Index ของเซ็ทที่จะคำคำนวณ* <b>listReduceProduct</b>
      <div>{{ listReduceProduct }}</div>

      <br />* <b>sumReduceDiscount</b>
      <div>{{ sumReduceDiscount }}</div>
    </div> -->

    <header class="header">
      POS by Rachchanon Dechyothin
    </header>

    <main>
      <Row :gutter="50">
        <Col
          :xs="{ span: 24, order: 2 }"
          :xl="{ span: 16, order: 1 }"
          class="product-zone"
        >
          <div class="block-container">
            <Row>
              <Col
                :xs="{ span: 24 }"
                :xl="{ span: 6 }"
                v-for="(e, index) in dataFromAPI"
                :key="index"
                v-match-heights="{ el: ['.book'] }"
              >
                <card class="book">
                  <div class="image">
                    <img :src="e.cover" :alt="e.title" />
                  </div>
                  <div class="title">{{ e.title }}</div>
                  <Divider dashed />
                  <Row type="flex" justify="center" align="middle">
                    <div class="price">{{ e.price }}</div>
                    <div class="currency">บาท</div>
                  </Row>

                  <Button type="success" size="large" @click="addToCart(e.id)">
                    Add to Cart
                  </Button>
                </card>
              </Col>
            </Row>
          </div>
        </Col>
        <Col
          :xs="{ span: 24, order: 1 }"
          :xl="{ span: 8, order: 2 }"
          class="listItem-zone"
        >
          <div class="block-container">
            <!-- {{ allItem }}
            <div>{{ listProduct }}</div>
            <div>{{ loopCount }}</div>
            <div>{{listProductById}}</div> -->

            <aside>
              <div>
                <card v-for="(e, index) in listProductById" :key="index">
                  <Row type="flex" justify="start" align="top">
                    <div class="image">
                      <img :src="e.cover" :alt="e.title" />
                    </div>

                    <div>
                      <div class="title">{{ e.title }}</div>
                      <Row type="flex" justify="start" align="middle">
                        <div class="price">{{ e.price }}</div>
                        <div class="currency">บาท</div>
                      </Row>
                    </div>

                    <div>
                      <Button @click="minusItem(e.id)">
                      -
                      </Button>
                      <Input disabled class="count-item" v-model="countItem[index]" clearable style="width: 40px" />
                      <Button @click="plusItem(e.id)">
                      +
                      </Button>
                      <Button type="error" @click="deleteItem(e.id)">
                      <Icon type="ios-trash-outline" size="24"/>
                      </Button>
                    </div>
                  </Row>
                </card>
              </div>
              <div>
                ส่วนลด: {{sumDiscount}}
              </div>
              <div>
                ราคารวมทั้งหมด: {{sumPrice - sumDiscount}}
              </div>
              <Button type="success" size="large" @click="xxx()"
                >คำนวณใหม่เพื่อรับส่วนลด</Button
              >
            </aside>
          </div>
        </Col>
      </Row>
    </main>
  </div>
</template>

<script>
export default {
  data() {
    return {
      dataFromAPI: "",
      allItem: [],
      allItemIndex: [],
      allItemById: [],
      listProductById: [],
      allListPrice: [],
      sumPrice: "",
      listItem: [],
      step2Filter: [],
      step2FilterById: [],
      step2ListPrice: [],
      step2ListIndex: [],
      step3Filter: [],
      listReduceDiscount: [],
      listReduceIndex: [],
      reduceById: [],
      listReduceProduct: [],
      sumReduceDiscount: [],
      sumDiscount: "",
      listProduct: [],
      loopCount: 0,
      countItem: []
    };
  },
  async mounted() {
    await this.fetchSomething();
    // await this.filterProdustDiscount();
    // await this.calcDiscount();
    // await this.calcPrice();

    // ["9781408855652","9781408855669","9781408855676","9781408855683","9781408855690","9781408855706","9781408855713"]
    // "9780241392362"
  },
  methods: {
    //ดึงข้อมูลจาก API
    async fetchSomething() {
      const res = await this.$axios.$get(
        "https://api.jsonbin.io/b/5f3154b06f8e4e3faf2f99de"
      );
      this.dataFromAPI = await res.books;
      console.log("fetchSomething -> dataFromAPI", this.dataFromAPI);
    },
    //กรองเฉพาะสินค้าที่มีส่วนลด
    async filterProdustDiscount() {
      // let allItem = [
      //   "9781408855652",
      //   "9781408855669",
      //   "9781408855652",
      //   "9781408855669",
      //   "9781408855669",
      //   "9780241392362"
      // ];
      // this.allItem = allItem;

      //รายการ id สินค้าที่มีส่วนลด
      let listProductDiscount = [
        "9781408855652",
        "9781408855669",
        "9781408855676",
        "9781408855683",
        "9781408855690",
        "9781408855706",
        "9781408855713"
      ];

      //กรองเอาเฉพาะรายการที่มีส่วนลด
      let step1Filter = [];

      this.allItem.forEach((e, index) => {
        listProductDiscount.forEach((e2, index) => {
          if (e == e2) {
            step1Filter.push(e2);
          }
        });
      });
      this.listItem = step1Filter.sortBy();
    },
    //คำนวณส่วนลด
    async calcDiscount() {
      // แยกรายการmที่มีส่วนลดเป็นเซ็ต
      let step2Filter = [];
      let step2ListPrice = [];
      let step2FilterById = [];
      let listPriceByID = [];

      //เช็คหาเซ็ทสินค้าที่ส่วนลด จากสินค้าทั้งหมด
      step2Filter = this.listItem.unique();
      this.step2Filter = step2Filter;
      console.log("calcDiscount -> this.step2Filter", this.step2Filter);

      this.step2Filter.forEach((e, index) => {
        step2FilterById.push(
          this.dataFromAPI.find(book => {
            return book.id == e;
          })
        );
        this.step2FilterById = step2FilterById;
      });

      console.log("calcDiscount -> this.step2FilterById", this.step2FilterById);

      listPriceByID = step2FilterById.clone();

      //เช็คหาราคาสินค้าที่ส่วนลด จากสินค้าทั้งหมด
      if (this.loopCount == 0) {
        this.step2FilterById.forEach((e, index) => {
          this.step2ListPrice.splice(index, 1, +e.price);
          // this.step2ListPrice.push(+e.price);
        });
      }
      if (this.loopCount >= 1) {
        this.step2FilterById.forEach((e, index) => {
          // this.step2ListPrice.splice(index, 1, +e.price)
          this.step2ListPrice.push(+e.price);
        });
      }

      //เช็คหา index ของราคาสินค้าที่ส่วนลด จากสินค้าทั้งหมด
      if (this.loopCount == 0) {
        this.step2Filter.forEach((e, index) => {
          let i = this.listItem.findIndex(e);
          this.step2ListIndex.splice(index, 1, i);
          // this.step2ListIndex.push(i);
        });
      }
      if (this.loopCount >= 1) {
        this.step2Filter.forEach((e, index) => {
          let i = this.listItem.findIndex(e);
          // this.step2ListIndex.splice(index, 1, i)
          this.step2ListIndex.push(i);
        });
      }
      this.step2ListIndex.sortBy().reverse();

      /* ============================================== */

      let step2Length = this.step2Filter.length;

      //หาผลรวมของราคาส่วนลด
      if (this.sumReduceDiscount.length == 0) {
        var totalPrice = this.step2ListPrice.sum();
      }
      if (this.sumReduceDiscount.length >= 1) {
        var totalPrice = this.sumReduceDiscount.sum();
      }

      console.log("หาผลรวมของราคาส่วนลด", totalPrice);

      //หาส่วนลดสำหรับ 2 ชิ้น discount 10%
      if (step2Length == 2) {
        totalPrice = totalPrice * 0.1;
        console.log("2 ชิ้น ลด", totalPrice);

        this.listReduceDiscount.push(totalPrice);
      }

      //หาส่วนลดสำหรับ 3 ชิ้น discount 11%
      if (step2Length == 3) {
        totalPrice = totalPrice * 0.11;
        console.log("3 ชิ้น ลด", totalPrice);

        this.listReduceDiscount.push(totalPrice);
      }

      //หาส่วนลดสำหรับ 4 ชิ้น discount 12%
      if (step2Length == 4) {
        totalPrice = totalPrice * 0.12;
        console.log("4 ชิ้น ลด", totalPrice);

        this.listReduceDiscount.push(totalPrice);
      }

      //หาส่วนลดสำหรับ 5 ชิ้น discount 13%
      if (step2Length == 5) {
        totalPrice = totalPrice * 0.13;
        console.log("5 ชิ้น ลด", totalPrice);

        this.listReduceDiscount.push(totalPrice);
      }

      //หาส่วนลดสำหรับ 6 ชิ้น discount 14%
      if (step2Length == 6) {
        totalPrice = totalPrice * 0.14;
        console.log("6 ชิ้น ลด", totalPrice);

        this.listReduceDiscount.push(totalPrice);
      }

      //หาส่วนลดสำหรับ 7 ชิ้น discount 15%
      if (step2Length == 7) {
        totalPrice = totalPrice * 0.15;
        console.log("7 ชิ้น ลด", totalPrice);

        this.listReduceDiscount.push(totalPrice);
      }

      this.sumDiscount = this.listReduceDiscount.sum();

      if (this.loopCount == 0) {
        if (step2Length <= 1 || step2Length >= 8) {
          this.sumDiscount = 0;
        }
      }
      if (this.loopCount > 0) {
        if (step2Length <= 1 || step2Length >= 8) {
          this.sumDiscount = 0;
        }
      }

      /* ============================================== */

      this.reduceListItem();
    },
    //หาเซ้ทส่วนลดที่เหลือว่ามีอีกมั้ย
    async reduceListItem() {
      let step3Filter = [];
      step3Filter = await this.listItem;

      let step3FilterForReduce = [];
      step3FilterForReduce = await this.listItem.clone();

      // ลบรายการที่แยกเซ็ตออกไป
      this.step2ListIndex.forEach((e, index) => {
        step3Filter.splice(e, 1);
      });

      //หา index ของรายการที่แยกเซ็ตออกไป
      this.listItem.forEach((e, index) => {
        let i = step3Filter.findIndex(e);
        this.listReduceIndex.push(i);
      });

      this.listItem.forEach((e, index) => {
        this.reduceById.push(
          this.dataFromAPI.find(book => {
            return book.id == e;
          })
        );
      });

      //เอาเฉพาะราคา ของรายการที่แยกเซ็ตออกไป
      console.log("777", this.reduceById);
      this.reduceById.forEach((e, index) => {
        // this.sumReduceDiscount.splice(index, 1, +e.price);
        this.sumReduceDiscount.push(+e.price);
      });

      // หา id ของรายการที่แยกเซ็ตออกไป
      this.listReduceIndex.forEach((e, index) => {
        // this.listReduceProduct.push(e)
        step3FilterForReduce.splice(e, 1);
      });

      this.listReduceProduct = step3FilterForReduce;

      this.step3Filter = await step3Filter.sortBy();

      /* ============================================== */

      //หาเซ็ทสินค้าที่ตรงเงื่อนไข
      let checkHaveDisCount = this.step3Filter.unique();
      // console.log("หาเซ็ทสินค้าที่ตรงเงื่อนไข", checkHaveDisCount);

      console.log("checkHaveDisCount.length", checkHaveDisCount.length);
      if (checkHaveDisCount.length == 0) {
        this.loopCount = 0;
      }
      if (checkHaveDisCount.length >= 1) {
        this.calcDiscount();
        this.calcPrice();
        this.loopCount = this.loopCount + 1;
        console.log("loopCount", this.loopCount);
      }
    },
    async calcPrice() {
      let allItemById = [];

      this.allItem.forEach((e, index) => {
        allItemById.push(
          this.dataFromAPI.find(book => {
            return book.id == e;
          })
        );
        this.allItemById = allItemById;
      });

      let listProductById = [];

      this.listProduct.forEach((e, index) => {
        listProductById.push(
          this.allItemById.find(book => {
            return book.id == e;
          })
        );
        this.listProductById = listProductById;
      });

      this.countItemInCart()

      //เอาเฉพาะราคาสินค้าทั้งหมดออกมา
      allItemById.forEach((e, index) => {
        this.allListPrice.splice(index, 1, +e.price);
      });

      //หาผลรวมของราคาสินค้าทั้งหมด
      let totalPrice = this.allListPrice.sum();
      this.sumPrice = totalPrice;

      let emptyList = this.listProduct.isEmpty()

      if (emptyList) {
        this.sumPrice = 0
      }

      //หา index ของ allItem
      this.allItem.forEach((e, index) => {
        let i = this.allItem.findIndex(e);
        this.allItemIndex.splice(index, 1, i);
      });
    },
    async addToCart(id) {
      this.allItem.push(id);

      //รายการสินค้าที่ใช้แสดงในตระกร้า
      this.listProduct = await this.allItem.unique();

      this.countItemInCart()
      // await this.filterProdustDiscount();
      // await this.calcDiscount();
      await this.calcPrice();
    },
    //นับจำนวนสินค้าในตรพกร้า
    countItemInCart() {
      this.allItem.count('a')
      this.listProduct.forEach((e, index) => {
        let count
        count = this.allItem.count(e)

        this.countItem[index] = count
      })
    },
    async minusItem(id) {
      let selectMinus = this.allItem.findIndex(id)
      this.allItem.removeAt(selectMinus)
      
      let emptyItem = this.allItem.count(id)
      
      if (emptyItem == (0)) {
        this.listProduct.remove(id)
      }
      await this.filterProdustDiscount();
      await this.calcDiscount();
      await this.calcPrice();
    },
    async plusItem(id) {
      let selectPlus = this.listProduct.findIndex(id)
      this.allItem.push(id)
      
      let emptyItem = this.listProduct.count(id)

      console.log('selectPlus', selectPlus);
      if (emptyItem == (0)) {
        this.listProduct.push(id)
      }
      // if (selectPlus == (-1)) {
      //   this.listProduct.push(id)
      // }
      await this.filterProdustDiscount();
      await this.calcDiscount();
      await this.calcPrice();
    },
    async deleteItem(id) {
      this.allItem.remove(id)
      this.listProduct.remove(id)

      let emptyItem = this.listProduct.count(id)

      if (emptyItem == (0)) {
        this.listProductById = []
      }
      await this.filterProdustDiscount();
      await this.calcDiscount();
      await this.calcPrice();

      // this.listProductById.forEach((e, index) => {
      //   Object.remove(e, id)
      //   console.log('e', e);
      // })
    },
    async xxx() {
      await this.filterProdustDiscount();
      await this.calcDiscount();
      await this.calcPrice();
    }
  }
};
</script>

<style lang="scss" scoped>
.container {
  background-image: linear-gradient(
    to right top,
    #051937,
    #004d7a,
    #008793,
    #00bf72,
    #a8eb12
  );
  min-height: 100vh;
}

.header {
  background: #070b0c;
  padding: 24px 50px;
  color: #515a6e;
  font-size: 14px;
}

main {
  padding: 50px;
}

.product-zone,
.listItem-zone {
  .block-container {
    background: rgba(245, 247, 249, 0.9);
    border-radius: 7px;
    min-height: 50vh;
    padding: 15px;
    max-width: 100%;
  }
}

.product-zone {
  .block-container {
    text-align: center;

    img {
      max-width: 100%;
      width: 152px;
      max-height: 220px;
      max-height: 243px;
    }

    .title {
      margin-bottom: 7px;
    }

    .price {
      font-weight: bold;
      color: #ff4c15;
      font-size: 1.3rem;
      display: inline-flex;
      margin-right: 5px;
    }

    .currency {
      display: inline-flex;
      color: #041920;
      font-weight: bold;
    }

    button {
      margin-top: 10px;
    }

    .ivu-btn-success {
      background-color: #041920;
    }

    .ivu-btn-success:hover {
      background-color: #073f52;
    }

    .ivu-btn-success.active,
    .ivu-btn-success:active {
      background-color: #1e612f;
    }

    .ivu-divider-horizontal {
      margin: 2px;
    }
  }
}

.listItem-zone {
  img {
    max-width: 100%;
    width: 75px;
    max-height: 220px;
    max-height: 243px;
    display: inline-flex;
    margin-right: 15px;
  }

  .title {
    display: inline-flex;
    font-size: 1.1rem;
    max-width: 230px;
  }

  .price {
    font-weight: bold;
    color: #ff4c15;
    font-size: 1.3rem;
    display: inline-flex;
    margin-right: 5px;
  }

  .currency {
    display: inline-flex;
    color: #041920;
    font-weight: bold;
  }
}

section.book {
  text-align: center;
}
</style>
