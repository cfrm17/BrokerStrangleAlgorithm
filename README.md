# Volatility Broker Strangle Algorithm

Broker Strangle Algorithm is used to calibrate FX volatility surface. At anchor expiry terms, the algorithm produces 25% and 10% Delta Call and Put volatilities from market at-the-money volatility, 25% and 10% Delta Risk Reversal and Broker Strangle volatility spreads.

The algorithm consists of solving a system of non-linear equations. When Left Delta quotation is used, nested non-linear equations are obtained as Left Delta leads by itself to solving a non-linear equation (see https://finpricing.com/lib/FiZeroBond.html).

Let us fix an anchor term and corresponding foreign and domestic interest rates (they will not appear in our notation, but they are crucial in this algorithm along with volatility data).

Denote by BS(β ,K,σ ) the Black-Scholes price formula for given strike K and volatility σ . Call/Put indicator β is defined as:β = 1 for Call and β = −1 for Put. Denote by Delta(β , K, σ ) be the Black-Scholes Delta formula for given strike K and volatility σ . 

Denote by Delta−1 (β ,σ ,δ ) the (correct) strike K solution of the equation Delta(β ,K , σ ) =δ for given volatility σ and real number δ (Left Delta is a heavy non-linear equation and must be treated carefully to keep notational burden light, we use Right Delta style notation/values everywhere, but the algorithm applies equally to Left Delta). 

Denote by ( ) ATM smile K σ σr Σ , ; the volatility associated to strike K for given ( ) .25 .25 .10 .10 , , , , − − σ σ = σ σ σ σ ATM smile r (at-the-money volatility and 25% Delta Call and Put volatilities). This function involves Delta−1 (β ,σ ,δ ) calculations, extrapolation points, and cubic spline interpolation. We just mention here that, by construction, we have:

 

The market does not quote the five volatilities ( ) .25 .25 .10 .10 , , , , − − σ σ = σ σ σ σ ATM smile r . Instead, it quotes ATM σ , .25 RR , .10 RR , .25 BSTR and .10 BSTR : ATM volatility, 25% and 10% Risk Reversal and Broker Strangle spreads. These quotes form the input of the Broker Strangle algorithm. We present here our understanding of what they actually mean


Let us denote by : , .25 .25 V BSTR ATM =σ + .10 .10 V : BSTR ATM =σ + . A market maker decides to sell/buy a 25% Delta Strangle contract (a package summing a 25% Delta Call and Put) for a dollar price. He then implies a number .25 BSTR that gives him this desired package dollar price when pricing the package with single volatility .25 V and associated strikes ˆ : ( ,0.25) .25 1 .25 K = Delta− V , ˆ : ( , 0.25) .25 1 .25 = − −−− K Delta V (called Broker Strangle strikes). 

It is this .25 BSTR number he then quotes. A similar definition applies to .10 BSTR : volatility .10 V and associated strikes ˆ : ( ,0.10).10 1 .10 K = Delta− V , ˆ : ( , 0.10).10 1 .10 = − −−− K Delta V price his Strangle contract to the desired target dollar price. Broker Strangle is thus quoted under smile symmetry assumption concerning itself with the deepness (distributional kurtosis) of the smile only.

The Risk Reversal spreads (smile slope/distributional skewness) come then into picture via a volatility interpolation method. A 25% Delta Risk Reversal contract is a package made of a long 25% Delta Call position and a short 25% Delta Put position. In order to price it, one needs two volatilities .25 .25 , − σ σ and associated strikes : ( ,0.25) .25 1 .25 K = Delta− σ ,: ( , 0.25) .25 1 .25 = − −−− K Delta σ . The .25 RR quote should capture the difference .25 −.25 σ −σ . 

Similarly, for the 10% Delta Risk Reversal contract pricing one needs two volatilities .10 .10 , − σ σ and associated strikes : ( ,0.10) .10 1 .10 K = Delta− σ , : ( , 0.10) .10 1 .10 = − −−− K Delta σ . The market maker quotes .25 RR and .10 RR so that together with his Broker Strangle quotes he obtains desired prices for the RR and BSTR contracts he sells/buys. That is, he knows .25 .25 , − σ σ , .25 BSTR and .10 .10 , − σ σ , .10 BSTR . To us, all that is visible is the quintuplet ATM σ , .25 RR , .10 RR , .25 BSTR and .10 BSTR . And we know it must satisfy the following relations:

 

This is a system of four linear equations and four unknowns .25 .25 .10 .10 , , , − − σ σ σ σ . Two of the
equations are linear, and the system can be re-written as a system of two non-linear equations and
two unknowns, say, , . the Broker Strangle algorithm takes as input .25 RR , .10 RR , .25 BSTR and
10 BSTR (along with ATM σ , term and interest rates), and obtains .25 .25 .10 .10 , , , − − σ σ σ σ by solving the system above. 

Once we have the volatilities the associated strikes .25 .25 .10 .10 ˆ , ˆ , ˆ , ˆ − − − K K K K are computed (they are different from .25 .25 .10 .10 , , , − − − K K K K . The system involves the Σ -interpolation which RBC FX Desk believes is closest to market standard. In MUREX language ( ) ATM ⋅ σ +σ −σ .25 −.25 0.5 and ( ) ATM ⋅ σ +σ −σ .10 −.10 0.5 are then the Real Strangles (STR).
