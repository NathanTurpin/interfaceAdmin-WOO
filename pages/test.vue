<template>
  <div class="container">
  <div class="row"> 
    <div class="block span3">
      <div class="product">
        <img src="http://placehold.it/295x190/333333/FFFFFF">
          <div class="buttons">
            <a class="buy btn btn-large btn-info" href="#"><i class="icon-shopping-cart"></i> Add to Cart</a>
            <a class="preview btn btn-large btn-info" href="#"><i class="icon-eye-open"></i> View item</a>
        </div>
      </div>
    
      <div class="info">
        <h4>Armillaria luteobubalina is a species of mushroom</h4>
        <span class="description">
          Rwanda is a country in central and eastern Africa located a few degrees south of the Ecuator.
        </span>
        <span class="price">$62.97</span>
        <a class="btn btn-info pull-right" href="#"><i class="icon-shopping-cart"></i> Buy Now</a>
      </div>
    
      <div class="details">
        <span class="time"><i class="icon-time"></i> 12 hours ago</span>
        <span class="rating pull-right">
          <span class="star"></span>
          <span class="star"></span>
          <span class="star"></span>
          <span class="star"></span>
          <span class="star"></span>
         </span>
      </div>
    </div>
  </div>
</div>
</template>

<script>
import test from "@/components/test/test";
import test1 from "@/components/test/test1";

export default {
  components: {
    test,
    test1,
  },
  data() {
    return {
      registerActive: false,

      token: "",
    };
  },
  mounted() {},
  methods: {},
};
</script>

<style scoped>


.block {
/* //	display: block;
	//position: relative;
	//width: 295px;
	//border-radius: 5px; */
	background: #fff;
	box-shadow: 0 1px 2px rgba(0, 0, 0, .2);
}

.product {
	display: block;
	position: relative;
}

.product img {
	width: 100%;
	border-top-left-radius: 5px;
	border-top-right-radius: 5px;
}

.info {
	display: block;
	position: relative;
	padding: 20px;
}

.details {
	border-top: 1px solid #e5e5e5;
	padding: 18px 20px;
}

.buttons {
	display: block;
	position: absolute;
	left: 0;
	top: 0;
	width: 100%;
	height: 100%;
	border-top-left-radius: 5px;
	border-top-right-radius: 5px;
	background: rgba(255, 255, 255, .5);
	opacity: 0;
	-webkit-transition: opacity .25s ease-in;
	-ms-transition: opacity .25s ease-in;
	-moz-transition: opacity .25s ease-in;
	-o-transition: opacity .25s ease-in;
	transition: opacity .25s ease-in;
}

.product:hover .buttons, .product:hover a {
	opacity: 1;
}

.buttons a {
	display: block;
	position: absolute;
	left: 50px;
	width: 155px;
	border-radius: 2px;
	/* //padding: 15px 10px 15px 65px;
	//font-family: Helvetica, sans-serif; */
	
	text-transform: uppercase;
	color: #fff;
	text-decoration: none;
	opacity: 0;
}

a.buy {
	top: 20%;
}

a.preview {
	bottom: 20%;
}

.info::after {
	display: block;
	position: absolute;
	top: -8px;
	left: 23px;
	content: "";
	width: 15px;
	height: 15px;
	background: #fff;
	-webkit-transform: rotate(45deg);
	-ms-transform: rotate(45deg);
	-moz-transform: rotate(45deg);
	-o-transform: rotate(45deg);
	transform: rotate(45deg);
}

.info h4 {
	position: relative;
	padding: 0 0 20px 0;
	margin: 0 0 20px 0;
	font-family: "Open Sans", sans-serif;
	font-weight: 700;
	font-size: 19px;
	line-height: 25px;
	color: #372f2b;
	letter-spacing: -1px;
}

.info h4::after {
	display: block;
	position: absolute;
	bottom: 0px;
	content: "";
	width: 40px;
	height: 2px;
	background: #3b86c4;
}

.info .description {
	display: block;
	padding-bottom: 20px;
	font-family: "Open Sans", sans-serif;
	font-size: 14px;
	font-weight: 600;
	color: #5f5f5f;
}

.info .price {
	font-family: "Open Sans", Helvetica, Arial, sans-serif;
	font-size: 24px;
	font-weight: 700;
	color: #372f2b;
	line-height: 26px;
}


.time {
	font-family: "Open Sans", sans-serif;
	font-size: 14px;
	font-weight: 700;
	color: #372f2b;

	background-position: 0 2px;
}

.rating {
    unicode-bidi: bidi-override;
    direction: rtl;
    font-size: 15px;
}

.rating span.star {
    font-family: FontAwesome;
    font-weight: 400;
    font-style: normal;
    display: inline-block;
}

.rating span.star:hover {
    cursor: pointer;
}

.rating span.star:before {
    content: "\f006";
    padding-right: 5px;
    color: #999;
}

.rating span.star:hover:before, .rating span.star:hover ~ span.star:before {
    content: "\f005";
    color: #e3cf7a;
}

</style>