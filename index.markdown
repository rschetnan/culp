---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults


layout: page
---
<script>
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
	if(document.getElementById("quotePercentage").firstChild == null)
	{
		document.getElementById("quotePercentage").appendChild(document.createTextNode(Math.round(quotePecentage).toString().concat("%")));
	}
	else
	{
		document.getElementById("quotePercentage").childNodes[0].nodeValue = Math.round(quotePecentage).toString().concat("%")
	}
	if(document.getElementById("basePercentage").firstChild == null)
	{
		document.getElementById("basePercentage").appendChild(document.createTextNode(Math.round(basePercentage).toString().concat("%")));
	}
	else
	{
		document.getElementById("basePercentage").childNodes[0].nodeValue = Math.round(basePercentage).toString().concat("%")
	}
	if(document.getElementById("quoteLiquidity").firstChild == null)
	{
		document.getElementById("quoteLiquidity").appendChild(document.createTextNode(quoteLiquidity));
	}
	else
	{
		document.getElementById("quoteLiquidity").childNodes[0].nodeValue = quoteLiquidity
	}
	if(document.getElementById("baseLiquidityCost").firstChild == null)
	{
		document.getElementById("baseLiquidityCost").appendChild(document.createTextNode(baseLiquidityCost));
	}
	else
	{
		document.getElementById("baseLiquidityCost").childNodes[0].nodeValue = baseLiquidityCost
	}
	if(document.getElementById("baseLiquidityCost").firstChild == null)
	{
		document.getElementById("totalLiquidityCost").appendChild(document.createTextNode(totalLiquidityCost));
	}
	else
	{
		document.getElementById("totalLiquidityCost").childNodes[0].nodeValue = totalLiquidityCost
	}
}
function setFieldFocus(fieldID)
{
	document.getElementById(fieldID).scrollIntoView();
	document.getElementById(fieldID).focus();
}
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
<a href="https://kyberswap.com/swap">KyberSwap</a> to calculate the liquidity needed for their liquidity positions.</p>
<h4>How to Calculate Concentrated Liquidity Pool Positions with CULP</h4>
<ul>
<li><a href="javascript:setFieldFocus('baseLiquidity')">Enter the amount of base liquidity</a>. For example, 
if you are opening a position with an ETH/USDC pair, enter the amount of ETH you are adding to your position.</li>
<li><a href="javascript:setFieldFocus('upperBound')">Enter the maximum base currency price</a>. For example, if you are opening a position with an ETH/USDC pair, enter the maximum price of ETH in USDC for your position.</li>
<li><a href="javascript:setFieldFocus('lowerBound')">Enter the minimum base currency price</a>. For example, if you are opening a position with an ETH/USDC pair, enter the minimum price of ETH in USDC for your position.</li>
<li><a href="javascript:setFieldFocus('currentPrice')">Enter the current base currency price</a>. For example, if you are opening a position with an ETH/USDC pair, enter the current price of ETH in USDC.</li>
<li><a href="javascript:setFieldFocus('calculate')">Click the Calculate button</a> to display the <a href="#amount-of-quote-liquidity">amount of quote liquidity</a> needed, the 
  <a href="#base-liquidity-cost">cost of the base liquidity</a>, the <a href="#total-cost">total cost</a> of the liquidity position, 
  the <a href="#percentage-of-base">percentage of base liquidity</a>, and the <a href="#percentage-of-quote">percentage of quote liquidity</a> for the position.</li>
</ul>
<form action="javascript:calculate()">
<table>
<tr id="enter-base-liquidity">
	<th>
		Amount of base liquidity to add:
	</th>
	<td>
		<input id="baseLiquidity" required/>
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
		<input id="upperBound" required/>
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
		<input id="lowerBound" required/>
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
		<input id="currentPrice" required/>
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
		<!-- input type="number" readonly="true" id="quoteLiquidity"/ -->
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
		<!-- input type="number" readonly="true" id="baseLiquidityCost"/ -->
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
		<!-- input type="number" readonly="true" id="totalLiquidityCost"/ -->
	</td>
	<td>
		<div class="tooltip">What's this?
			<span class="tooltiptext">The total cost of the position in quote currency. For example, if you are opening a position
				with an ETH/USDC pair, this is the total cost of your position in USDC.</span>
		</div>
	</td>
</tr>
<tr id="percentage-of-base">
	<th>
		Percentage of base liquidity in position:
	</th>
	<td id="basePercentage">
		<!-- input type="number" readonly="true" id="basePercentage"/ -->
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
		<!-- input type="number" readonly="true" id="quotePercentage"/ -->
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
<p>The <a href="https://rschetnan.github.io/culp/">CULP</a> calculator is provided under the <a href="https://opensource.org/licenses/MIT">MIT License</a> as stated in the <a href="/culp/about/">About page</a>.
See the <a href="/culp/about/">About page</a> for more information about licensing, copyright, and methodology.</p>

