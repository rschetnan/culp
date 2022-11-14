---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults


layout: page
---
<script>
function getUpdatedLiquidityValues(baseAmount, quoteAmount, originalPrice, updatedPrice, minPrice, maxPrice)
{
	let lx = quoteAmount / (Math.sqrt(originalPrice) - Math.sqrt(minPrice));
	let ly = baseAmount * Math.sqrt(originalPrice) * Math.sqrt(maxPrice) / (Math.sqrt(maxPrice) - Math.sqrt(originalPrice));
	let l = lx < ly ? lx : ly;
	let liquidityY = l * (Math.sqrt(updatedPrice) - Math.sqrt(minPrice));
	let liquidityX = l * ((Math.sqrt(maxPrice) - Math.sqrt(updatedPrice)) / (Math.sqrt(updatedPrice) * Math.sqrt(maxPrice)));
	return {
		baseLiquidity: liquidityX,
		quoteLiquidity: liquidityY
	}
};

function calculatePriceChange()
{
	let baseAmount = eval(document.getElementById("baseAmountForPriceChange").value);
	let quoteAmount = eval(document.getElementById("quoteAmountForPriceChange").value);
	let minPrice = eval(document.getElementById("minPriceForPriceChange").value);
	let maxPrice = eval(document.getElementById("maxPriceForPriceChange").value);
	let originalPrice = eval(document.getElementById("originalPriceForPriceChange").value);
	let updatedPrice = eval(document.getElementById("updatedPriceForPriceChange").value);
	
	let updatedLiquidity = getUpdatedLiquidityValues(baseAmount, quoteAmount, originalPrice, updatedPrice, minPrice, maxPrice);

	let originalValue = baseAmount * originalPrice + quoteAmount;
	let updatedValue = updatedLiquidity.baseLiquidity * updatedPrice + updatedLiquidity.quoteLiquidity;
	document.getElementById("updatedBaseAmountforPriceChange").childNodes[0].nodeValue = updatedLiquidity.baseLiquidity;
	document.getElementById("updatedQuoteAmountforPriceChange").childNodes[0].nodeValue = updatedLiquidity.quoteLiquidity;
	document.getElementById("originalPositionValueforPriceChange").childNodes[0].nodeValue = originalValue;
	document.getElementById("updatedPositionValueforPriceChange").childNodes[0].nodeValue = updatedValue;
};

function calculateMinPrice()
{
	let baseAmount = eval(document.getElementById("baseAmountForMinCalculation").value);
	let quoteAmount = eval(document.getElementById("quoteAmountForMinCalculation").value);
	let maxPrice = eval(document.getElementById("maxPriceForMinCalculation").value);
	let currentPrice = eval(document.getElementById("currentPriceForMinCalculation").value);
	let topLiquidity = baseAmount * Math.sqrt(currentPrice) * Math.sqrt(maxPrice) / (Math.sqrt(maxPrice) - Math.sqrt(currentPrice));
	let sqrtMinPrice = Math.sqrt(currentPrice) - quoteAmount / topLiquidity;
	let minPrice = Math.pow(sqrtMinPrice, 2)
	document.getElementById("minPriceForMinCalculation").childNodes[0].nodeValue = minPrice;
};

function calculateMaxPrice()
{
	let baseAmount = eval(document.getElementById("baseAmountForMaxCalculation").value);
	let quoteAmount = eval(document.getElementById("quoteAmountForMaxCalculation").value);
	let minPrice = eval(document.getElementById("minPriceForMaxCalculation").value);
	let currentPrice = eval(document.getElementById("currentPriceForMaxCalculation").value);
	let bottomLiquidity = quoteAmount / (Math.sqrt(currentPrice) - Math.sqrt(minPrice));
	let sqrtMaxPrice = bottomLiquidity * Math.sqrt(currentPrice) / (bottomLiquidity - Math.sqrt(currentPrice) * baseAmount);
	let maxPrice = Math.pow(sqrtMaxPrice, 2);
	document.getElementById("maxPriceForMaxCalculation").childNodes[0].nodeValue = maxPrice;
};

function calculate()
{
	let baseLiquidity = eval(document.getElementById("baseLiquidity").value);
	let upperBound = eval(document.getElementById("upperBound").value);
	let lowerBound = eval(document.getElementById("lowerBound").value);
	let currentPrice = eval(document.getElementById("currentPrice").value);
	let liquidityX = baseLiquidity * Math.sqrt(currentPrice) * Math.sqrt(upperBound) / (Math.sqrt(upperBound) - Math.sqrt(currentPrice))
	let quoteLiquidity = liquidityX * (Math.sqrt(currentPrice) - Math.sqrt(lowerBound));
	let baseLiquidityCost = baseLiquidity * currentPrice;
	let totalLiquidityCost = quoteLiquidity + baseLiquidityCost;
	let quotePecentage = quoteLiquidity / totalLiquidityCost * 100;
	let basePercentage = baseLiquidityCost /  totalLiquidityCost * 100;

	let liquidityAtMinPrice = getUpdatedLiquidityValues(baseLiquidity, quoteLiquidity, currentPrice, lowerBound, lowerBound, upperBound);
	let liquidityAtMaxPrice = getUpdatedLiquidityValues(baseLiquidity, quoteLiquidity, currentPrice, upperBound, lowerBound, upperBound);
	
	document.getElementById("quotePercentage").childNodes[0].nodeValue = Math.round(quotePecentage).toString().concat("%");
	document.getElementById("basePercentage").childNodes[0].nodeValue = Math.round(basePercentage).toString().concat("%");
	document.getElementById("quoteLiquidity").childNodes[0].nodeValue = quoteLiquidity;
	document.getElementById("baseLiquidityCost").childNodes[0].nodeValue = baseLiquidityCost;
	document.getElementById("totalLiquidityCost").childNodes[0].nodeValue = totalLiquidityCost;
	document.getElementById("valueAtMinPrice").childNodes[0].nodeValue = liquidityAtMinPrice.baseLiquidity * lowerBound;
	document.getElementById("valueAtMaxPrice").childNodes[0].nodeValue = liquidityAtMaxPrice.quoteLiquidity;
};

</script>
<style>
th {
	text-align: right;
}
.tooltip {
	position: relative;
	display: inline-block;
	border-bottom: 1px dotted black;
}

.tooltip .tooltiptext {
	visibility: hidden;
	width: 120px;
	background-color: black;
	color: #fff;
	text-align: left;
	border-radius: 6px;
	padding: 5px 0;
	
	/* Position the tooltip */
	position: absolute;
	z-index: 1;
	top: -5px;
	left: 105%;
}

.tooltip:hover .tooltiptext {
visibility: visible;
}

</style>
<p>CULP helps liquidity providers for concentrated liquidity pools such as those on <a href="https://uniswap.org/">Uniswap V3</a> and 
<a href="https://kyberswap.com/swap">KyberSwap</a> to calculate their liquidity positions.</p>

<ul>
	<li><a href="#calculate-position">Calculate Liquidity for a Concentrated Liquidity Pool Position</a></li>
	<li><a href="#calculate-maximum">Calculate the Maximum Price for a Position</a></li>
	<li><a href="#calculate-minimum">Calculate the Minimum Price for a Position</a></li>
	<li><a href="#calculate-value">Calculate Position Value Following a Price Change</a></li>
</ul>

<h4><a id="calculate-position">Calculate Liquidity for a Concentrated Liquidity Pool Position</a></h4>
<form action="javascript:calculate()">
<table>
<tr id="enter-base-liquidity">
	<th>
		Amount of base liquidity to add:
	</th>
	<td>
		<input id="baseLiquidity" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The amount of base liquidity. For example, if you are opening a position
				with an ETH/USDC pair, this is the amount of ETH you are adding to your position.
			</span>
		</div>
	</td>
</tr>
<tr id="enter-maximum">
	<th>Maximum price:</th>
	<td>
		<input id="upperBound" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The maximum base currency price. For example, if you are opening a position
				with an ETH/USDC pair, this is the maximum price of ETH in USDC for your position.</span>
		</div>
	</td>
</tr>
<tr id="enter-minimum">
	<th>
		Minimum price:
	</th>
	<td>
		<input id="lowerBound" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The minimum base currency price. For example, if you are opening a position
				with an ETH/USDC pair, this is the minimum price of ETH in USDC for your position.</span>
		</div>
	</td>
</tr>
<tr id="enter-current">
	<th>
		Current price:
	</th>
	<td>
		<input id="currentPrice" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The current base currency price. For example, if you are opening a position
				with an ETH/USDC pair, this is the current price of ETH in USDC.</span>
		</div>
	</td>
</tr>
<tr id="click-calculate">
	<th>Get results:</th>
	<td colspan="2"><input id="calculate" type="submit" value="Calculate"></td>
</tr>
<tr id="amount-of-quote-liquidity">
	<th>
		Amount of quote liquidity to add:
	</th>
	<td id="quoteLiquidity">
		&nbsp;<!-- input type="number" readonly="true" id="quoteLiquidity"/ -->
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The amount of quote liquidity to add. For example, if you are opening a position
				with an ETH/USDC pair, this is the amount of USDC to add to your position.</span>
		</div>
	</td>
</tr>
<tr id="base-liquidity-cost">
	<th>
		Base liquidity cost:
	</th>
	<td id="baseLiquidityCost">
		&nbsp;<!-- input type="number" readonly="true" id="baseLiquidityCost"/ -->
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The cost of the base liquidity in quote currency. For example, if you are opening a position
				with an ETH/USDC pair, this is the cost of the ETH in USDC for your position.</span>
		</div>
	</td>
</tr>
<tr id="total-cost">
	<th>
		Total cost of liquidity position:
	</th>
	<td id="totalLiquidityCost">
		&nbsp;<!-- input type="number" readonly="true" id="totalLiquidityCost"/ -->
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The total cost of the position in quote currency. For example, if you are opening a position
				with an ETH/USDC pair, this is the total cost of your position in USDC.</span>
		</div>
	</td>
</tr>
<tr id="value-at-bottom-of-range">
	<th>
		Value of position at the bottom of the price range:
	</th>
	<td id="valueAtMinPrice">
		&nbsp;<!-- input type="number" readonly="true" id="totalLiquidityCost"/ -->
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The value of the position at the bottom of the price range. For example, if you are opened a position
				with an ETH/USDC pair, this is the value of your position in USDC. At the bottom of the price range, your liquidity would be all
				in ETH.</span>
		</div>
	</td>
</tr>
<tr id="value-at-top-of-range">
	<th>
		Value of position at the top of the price range:
	</th>
	<td id="valueAtMaxPrice">
		&nbsp;<!-- input type="number" readonly="true" id="totalLiquidityCost"/ -->
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The value of the position at the top of the price range. For example, if you are opened a position
				with an ETH/USDC pair, this is the value of your position in USDC.</span>
		</div>
	</td>
</tr>

<tr id="percentage-of-base">
	<th>
		Percentage of base liquidity in position:
	</th>
	<td id="basePercentage">
		&nbsp;<!-- input type="number" readonly="true" id="basePercentage"/ -->
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The percentage of base liquidity in the position. For example, if you are opening a position
				with an ETH/USDC pair, this is the percentage of ETH in your position.</span>
		</div>
	</td>
</tr>
<tr id="percentage-of-quote">
	<th>
		Percentage of quote liquidity in position:
	</th>
	<td id="quotePercentage">
		&nbsp;<!-- input type="number" readonly="true" id="quotePercentage"/ -->
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The percentage of quote liquidity in the position. For example, if you are opening a position
				with an ETH/USDC pair, this is the percentage of USDC in your position.</span>
		</div>
	</td>
</tr>
</table>
</form>
<h4><a id="calculate-maximum">Calculate the Maximum Price for a Position</a></h4>
<form action="javascript:calculateMaxPrice()">
<table>
<tr id="enter-base-amount-for-max">
	<th>
		Amount of base liquidity:
	</th>
	<td>
		<input id="baseAmountForMaxCalculation" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The amount of base liquidity. For example, if you open a position
				with an ETH/USDC pair, this is the amount of ETH in your position.
			</span>
		</div>
	</td>
</tr>
<tr id="enter-quote-amount-for-max">
	<th>
		Amount of quote liquidity:
	</th>
	<td id="quoteLiquidity">
		<input id="quoteAmountForMaxCalculation" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The amount of quote liquidity. For example, if you open a position
				with an ETH/USDC pair, this is the amount of USDC in your position.</span>
		</div>
	</td>
</tr>

<tr id="enter-min-amount-for-max">
	<th>
		Minimum price:
	</th>
	<td>
		<input id="minPriceForMaxCalculation" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The minimum base currency price. For example, if you are opening a position
				with an ETH/USDC pair, this is the minimum price of ETH in USDC for your position.</span>
		</div>
	</td>
</tr>
<tr id="enter-current-for-max">
	<th>
		Current price:
	</th>
	<td>
		<input id="currentPriceForMaxCalculation" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The current base currency price. For example, if you are opening a position
				with an ETH/USDC pair, this is the current price of ETH in USDC.</span>
		</div>
	</td>
</tr>
<tr id="click-calculate-for-max">
	<th>Get results:</th>
	<td colspan="2"><input id="calculateMaximum" type="submit" value="Calculate"></td>
</tr>
<tr id="maximum-price-for-max">
	<th>
		Maximum price for position:
	</th>
	<td id="maxPriceForMaxCalculation">
		&nbsp;<!-- input type="number" readonly="true" id="quoteLiquidity"/ -->
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The maximum base currency price. For example, if you open a position
				with an ETH/USDC pair, this is the maximum price of ETH in USDC for your position.</span>
		</div>
	</td>
</tr>
</table>
</form>
<h4><a id="calculate-minimum">Calculate the Minimum Price for a Position</a></h4>
<form action="javascript:calculateMinPrice()">
<table>
<tr id="enter-base-amount-for-min">
	<th>
		Amount of base liquidity:
	</th>
	<td>
		<input id="baseAmountForMinCalculation" required/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The amount of base liquidity. For example, if you open a position
				with an ETH/USDC pair, this is the amount of ETH in your position.
			</span>
		</div>
	</td>
</tr>
<tr id="enter-quote-amount-for-min">
	<th>
		Amount of quote liquidity:
	</th>
	<td id="quoteLiquidity">
		<input id="quoteAmountForMinCalculation" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The amount of quote liquidity. For example, if you open a position
				with an ETH/USDC pair, this is the amount of USDC in your position.</span>
		</div>
	</td>
</tr>

<tr id="enter-max-amount-for-min">
	<th>
		Maximum price:
	</th>
	<td>
		<input id="maxPriceForMinCalculation" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The maximum base currency price. For example, if you open a position
				with an ETH/USDC pair, this is the maximum price of ETH in USDC for your position.</span>
		</div>
	</td>
</tr>
<tr id="enter-current-for-min">
	<th>
		Current price:
	</th>
	<td>
		<input id="currentPriceForMinCalculation" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The current base currency price. For example, if you are opening a position
				with an ETH/USDC pair, this is the current price of ETH in USDC.</span>
		</div>
	</td>
</tr>
<tr id="click-calculate-for-min">
	<th>Get results:</th>
	<td colspan="2"><input id="calculateMaximum" type="submit" value="Calculate"></td>
</tr>
<tr id="minimum-price-for-min">
	<th>
		Minimum price for position:
	</th>
	<td id="minPriceForMinCalculation">
		&nbsp;<!-- input type="number" readonly="true" id="quoteLiquidity"/ -->
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The minimum base currency price. For example, if you are opening a position
				with an ETH/USDC pair, this is the minimum price of ETH in USDC for your position.</span>
		</div>
	</td>
</tr>
</table>
</form>

<h4><a id="calculate-value">Calculate Position Value Following a Price Change</a></h4>
<form action="javascript:calculatePriceChange()">
<table>
<tr id="enter-base-amount-for-price-change">
	<th>
		Amount of base liquidity:
	</th>
	<td>
		<input id="baseAmountForPriceChange" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The amount of base liquidity. For example, if you open a position
				with an ETH/USDC pair, this is the amount of ETH in your position.
			</span>
		</div>
	</td>
</tr>
<tr id="enter-quote-amount-for-price-change">
	<th>
		Amount of quote liquidity:
	</th>
	<td id="quoteLiquidity">
		<input id="quoteAmountForPriceChange" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The amount of quote liquidity. For example, if you opened a position
				with an ETH/USDC pair, this is the amount of USDC in your position.</span>
		</div>
	</td>
</tr>
<tr id="enter-min-amount-for-price-change">
	<th>
		Minimum price:
	</th>
	<td>
		<input id="minPriceForPriceChange" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The minimum base currency price. For example, if you opened a position
				with an ETH/USDC pair, this is the minimum price of ETH in USDC for your position.</span>
		</div>
	</td>
</tr>

<tr id="enter-max-amount-for-price-change">
	<th>
		Maximum price:
	</th>
	<td>
		<input id="maxPriceForPriceChange" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The maximum base currency price. For example, if you opened a position
				with an ETH/USDC pair, this is the maximum price of ETH in USDC for your position.</span>
		</div>
	</td>
</tr>
<tr id="enter-original-for-price-change">
	<th>
		Original price:
	</th>
	<td>
		<input id="originalPriceForPriceChange" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The original base currency price. For example, if you opened a position
				with an ETH/USDC pair, this is the original price of ETH in USDC.</span>
		</div>
	</td>
</tr>
<tr id="enter-updated-for-price-change">
	<th>
		Updated price:
	</th>
	<td>
		<input id="updatedPriceForPriceChange" required type="number" step="0.0000001"/>
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The updated base currency price. For example, if you opened a position
				with an ETH/USDC pair, this is the updated price of ETH in USDC.</span>
		</div>
	</td>
</tr>
<tr id="click-calculate-for-min">
	<th>Get results:</th>
	<td colspan="2"><input id="calculateMaximum" type="submit" value="Calculate"></td>
</tr>
<tr id="updated-base-amount-for-price-change">
	<th>
		Updated base liquidity amount:
	</th>
	<td id="updatedBaseAmountforPriceChange">
		&nbsp;<!-- input type="number" readonly="true" id="quoteLiquidity"/ -->
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The updated amount of base liquidity following price change. For example, if you open a position
				with an ETH/USDC pair, this is the amount of ETH in your position following the price change.
			</span>
		</div>
	</td>
</tr>
<tr id="updated-quote-amount-for-price-change">
	<th>
		Updated quote liquidity amount:
	</th>
	<td id="updatedQuoteAmountforPriceChange">
		&nbsp;<!-- input type="number" readonly="true" id="quoteLiquidity"/ -->
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The updated amount of quote liquidity following price change. For example, if you open a position
				with an ETH/USDC pair, this is the amount of USDC in your position following the price change.
			</span>
		</div>
	</td>
</tr>
<tr id="original-position-value-for-price-change">
	<th>
		Position value before price change:
	</th>
	<td id="originalPositionValueforPriceChange">
		&nbsp;<!-- input type="number" readonly="true" id="quoteLiquidity"/ -->
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The original value of the position prior to the price change.
			</span>
		</div>
	</td>
</tr>
<tr id="updated-position-value-for-price-change">
	<th>
		Position value following price change:
	</th>
	<td id="updatedPositionValueforPriceChange">
		&nbsp;<!-- input type="number" readonly="true" id="quoteLiquidity"/ -->
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The updated value of the position following the price change.
			</span>
		</div>
	</td>
</tr>
</table>
</form>

<p>The <a href="https://rschetnan.github.io/culp/">CULP</a> calculator is provided under the <a href="https://opensource.org/licenses/MIT">MIT License</a> as stated in the <a href="/culp/about/">About page</a>.
See the <a href="/culp/about/">About page</a> for more information about licensing, copyright, and methodology.</p>

