---
title: My Oral Presentations
permalink: /talks/
---


<head>
<title>JabRef references</title>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<script type="text/javascript">
<!--
// QuickSearch script for JabRef HTML export 
// Version: 3.0
//
// Copyright (c) 2006-2011, Mark Schenk
//
// This software is distributed under a Creative Commons Attribution 3.0 License
// http://creativecommons.org/licenses/by/3.0/
//
// Features:
// - intuitive find-as-you-type searching
//    ~ case insensitive
//    ~ ignore diacritics (optional)
//
// - search with/without Regular Expressions
// - match BibTeX key
//

function loadTableData() {
	// find table and appropriate rows
	searchTable = document.getElementById('qs_table');
	var allRows = searchTable.getElementsByTagName('tbody')[0].getElementsByTagName('tr');

	// split all rows into entryRows and infoRows (e.g. abstract, review, bibtex)
	entryRows = new Array(); infoRows = new Array(); absRows = new Array(); revRows = new Array();

	// get data from each row
	entryRowsData = new Array(); absRowsData = new Array(); revRowsData = new Array(); 
	
	BibTeXKeys = new Array();
	
	for (var i=0, k=0, j=0; i<allRows.length;i++) {
		if (allRows[i].className.match(/entry/)) {
			entryRows[j] = allRows[i];
			entryRowsData[j] = stripDiacritics(getTextContent(allRows[i]));
			allRows[i].id ? BibTeXKeys[j] = allRows[i].id : allRows[i].id = 'autokey_'+j;
			j ++;
		} else {
			infoRows[k++] = allRows[i];
			// check for abstract/review
			if (allRows[i].className.match(/abstract/)) {
				absRows.push(allRows[i]);
				absRowsData[j-1] = stripDiacritics(getTextContent(allRows[i]));
			} else if (allRows[i].className.match(/review/)) {
				revRows.push(allRows[i]);
				revRowsData[j-1] = stripDiacritics(getTextContent(allRows[i]));
			}
		}
	}
	//number of entries and rows
	numEntries = entryRows.length;
	numInfo = infoRows.length;
	numAbs = absRows.length;
	numRev = revRows.length;
}


// Strip Diacritics from text
// http://stackoverflow.com/questions/990904/javascript-remove-accents-in-strings

// String containing replacement characters for stripping accents 
var stripstring = 
    'AAAAAAACEEEEIIII'+
    'DNOOOOO.OUUUUY..'+
    'aaaaaaaceeeeiiii'+
    'dnooooo.ouuuuy.y'+
    'AaAaAaCcCcCcCcDd'+
    'DdEeEeEeEeEeGgGg'+
    'GgGgHhHhIiIiIiIi'+
    'IiIiJjKkkLlLlLlL'+
    'lJlNnNnNnnNnOoOo'+
    'OoOoRrRrRrSsSsSs'+
    'SsTtTtTtUuUuUuUu'+
    'UuUuWwYyYZzZzZz.';

function stripDiacritics(str){

    if(noSquiggles==false){
        return str;
    }

    var answer='';
    for(var i=0;i<str.length;i++){
        var ch=str[i];
        var chindex=ch.charCodeAt(0)-192;   // Index of character code in the strip string
        if(chindex>=0 && chindex<stripstring.length){
            // Character is within our table, so we can strip the accent...
            var outch=stripstring.charAt(chindex);
            // ...unless it was shown as a '.'
            if(outch!='.')ch=outch;
        }
        answer+=ch;
    }
    return answer;
}

// http://stackoverflow.com/questions/3446170/escape-string-for-use-in-javascript-regex
// NOTE: must escape every \ in the export code because of the JabRef Export...
function escapeRegExp(str) {
  return str.replace(/[-\[\]\/\{\}\(\)\*\+\?\.\\\^\$\|]/g, "\\$&");
}

function toggleInfo(articleid,info) {

	var entry = document.getElementById(articleid);
	var abs = document.getElementById('abs_'+articleid);
	var rev = document.getElementById('rev_'+articleid);
	var bib = document.getElementById('bib_'+articleid);
	
	if (abs && info == 'abstract') {
		abs.className.indexOf('noshow') == -1?abs.className = 'abstract noshow':abs.className = 'abstract show';
	} else if (rev && info == 'review') {
		rev.className.indexOf('noshow') == -1?rev.className = 'review noshow':rev.className = 'review show';
	} else if (bib && info == 'bibtex') {
		bib.className.indexOf('noshow') == -1?bib.className = 'bibtex noshow':bib.className = 'bibtex show';
	} else { 
		return;
	}

	// check if one or the other is available
	var revshow; var absshow; var bibshow;
	(abs && abs.className.indexOf('noshow') == -1)? absshow = true: absshow = false;
	(rev && rev.className.indexOf('noshow') == -1)? revshow = true: revshow = false;	
	(bib && bib.className.indexOf('noshow') == -1)? bibshow = true: bibshow = false;
	
	// highlight original entry
	if(entry) {
		if (revshow || absshow || bibshow) {
		entry.className = 'entry highlight show';
		} else {
		entry.className = 'entry show';
		}
	}
	
	// When there's a combination of abstract/review/bibtex showing, need to add class for correct styling
	if(absshow) {
		(revshow||bibshow)?abs.className = 'abstract nextshow':abs.className = 'abstract';
	} 
	if (revshow) {
		bibshow?rev.className = 'review nextshow': rev.className = 'review';
	}	
	
}

function setStatistics (hits) {
	if(hits < 0) { hits=numEntries; }
	if(stats) { stats.firstChild.data = hits + '/' + numEntries}
}

function getTextContent(node) {
	// Function written by Arve Bersvendsen
	// http://www.virtuelvis.com
	
	if (node.nodeType == 3) {
	return node.nodeValue;
	} // text node
	if (node.nodeType == 1 && node.className != "infolinks") { // element node
	var text = [];
	for (var chld = node.firstChild;chld;chld=chld.nextSibling) {
		text.push(getTextContent(chld));
	}
	return text.join("");
	} return ""; // some other node, won't contain text nodes.
}

function showAll(){
	closeAllInfo();
	for (var i = 0; i < numEntries; i++){ entryRows[i].className = 'entry show'; }
}

function closeAllInfo(){
	for (var i=0; i < numInfo; i++){
		if (infoRows[i].className.indexOf('noshow') ==-1) {
			infoRows[i].className = infoRows[i].className + ' noshow';
		}
	}
}

function clearQS() {
	qsfield.value = '';
	showAll();
}

function redoQS(){
	showAll();
	quickSearch(qsfield);
}


function toggleSettings(){
	var togglebutton = document.getElementById('showsettings');
	var settings = document.getElementById('settings');
	
	if(settings.className == "hidden"){
		settings.className = "show";
		togglebutton.innerText = "close settings";
		togglebutton.textContent = "close settings";
	}else{
		settings.className = "hidden";
		togglebutton.innerText = "settings...";		
		togglebutton.textContent = "settings...";
	}
}

-->
</script>
<style type="text/css">

div#settings ul {margin: 0; padding: 0; }
div#settings li {margin: 0; padding: 0 1em 0 0; display: inline; list-style: none; }
div#settings li + li { border-left: 2px #efefef solid; padding-left: 0.5em;}
div#settings input { margin-bottom: 0px;}

div#settings.hidden {display:none;}

#showsettings { border: 1px grey solid; padding: 0 0.5em; float:right; line-height: 1.6em; text-align: right; }
#showsettings:hover { cursor: pointer; }

table { border: 1px gray none; width: 100%; empty-cells: show; border-spacing: 0em 0.1em; margin: 1em 0em; }
th, td { border: none; padding: 0.5em; vertical-align: top; }

td a { color: MediumBlue; text-decoration: none; }
td a:hover  { text-decoration: underline; }

tr.noshow { display: none;}
tr.highlight td { }
tr.abstract td, tr.review td, tr.bibtex td { background-color: LightYellow; text-align: justify; border-bottom:  2px #2E2E2E solid ; border-top: 1px #2E2E2E dashed; font-size: 11px;}
tr.nextshow td { border-bottom-style: none; }

tr.bibtex pre { width: 100%; overflow: auto; white-space: pre-wrap;}
p.infolinks { margin: 0.3em 0em 0em 0em; padding: 0px; }

@media print {
	p.infolinks, #qs_settings, #quicksearch, t.bibtex { display: none !important; }
	tr { page-break-inside: avoid; }
}
</style>
</head>
<body>

<table id="qs_table" border="1">
<tbody>
<tr id="Talk-SNSF2020" class="entry">
	<td>
	<b>[SNSF2020]</b>
	SNSF Fellows Conference<br>
	Virtual, July 2020.<br>
	"Scalable semidefinite programming"<br>
	Joint work with: 
	Cevher, V., Fercoq, O., Udell, M. and Tropp, J.A. 
	<p class="infolinks">
	[<a href="javascript:toggleInfo('Talk-SNSF2020','abstract')">Abstract</a>]
	 [<a href="https://drive.google.com/file/d/1F-AG_jqXCJb3pcIDGTqrJIBXwN2XJwuR/view" target="_blank">Slides</a>]
	
	</p>
	</td>
</tr>
<tr id="abs_Talk-SNSF2020" class="abstract noshow">
	<td><b>Abstract</b>: Semidefinite programming (SDP) is a powerful framework from convex optimization that has striking potential for data science applications. This paper develops a provably correct algorithm for solving large SDP problems by economizing on both the storage and the arithmetic costs. Numerical evidence shows that the method is effective for a range of applications, including relaxations of MaxCut, abstract phase retrieval, and quadratic assignment. Running on a laptop, the algorithm can handle SDP instances where the matrix variable has over 10^13 entries.</td>
</tr><tr id="Talk-ICML2019a" class="entry">
	<td>
	<b>[ICML2019]</b>
	36th International Conference on Machine Learning<br>
	Long Beach, California, USA, June 2019.<br>
	"Conditional gradient methods via stochastic path-integrated differential estimator"<br>
	Joint work with: 
	Sra, S. and Cevher, V. 
	<p class="infolinks">
	[<a href="javascript:toggleInfo('Talk-ICML2019a','abstract')">Abstract</a>]
	 [<a href="https://drive.google.com/file/d/1wnfR1JqB_bnpRlBQHQFzELuVXy_nbsIR/view" target="_blank">Slides</a>]
	
	</p>
	</td>
</tr>
<tr id="abs_Talk-ICML2019a" class="abstract noshow">
	<td><b>Abstract</b>: We propose a class of variance-reduced stochastic conditional gradient methods. By adopting the recent stochastic path-integrated differential estimator technique (SPIDER) of Fang et. al. (2018) for the classical Frank-Wolfe (FW) method, we introduce SPIDER-FW for finite-sum minimization as well as the more general expectation minimization problems. SPIDER-FW enjoys superior complexity guarantees in the non-convex setting, while matching the best known FW variants in the convex case. We also extend our framework a la conditional gradient sliding (CGS) of Lan &amp; Zhou. (2016), and propose SPIDER-CGS.</td>
</tr><tr id="Talk-ICML2019b" class="entry">
	<td>
	<b>[ICML2019]</b>
	36th International Conference on Machine Learning<br>
	Long Beach, California, USA, June 2019.<br>
	"A conditional-gradient-based augmented Lagrangian framework"<br>
	Joint work with: 
	Fercoq, O. and Cevher, V. 
	<p class="infolinks">
	[<a href="javascript:toggleInfo('Talk-ICML2019b','abstract')">Abstract</a>]
	 [<a href="https://drive.google.com/file/d/1eaMUf7dzYxxk8ZJA38cp7zyxpp-ZhJYr/view" target="_blank">Slides</a>]
	
	</p>
	</td>
</tr>
<tr id="abs_Talk-ICML2019b" class="abstract noshow">
	<td><b>Abstract</b>: This paper considers a generic convex minimization template with affine constraints over a compact domain, which covers key semidefinite programming applications. The existing conditional gradient methods either do not apply to our template or are too slow in practice. To this end, we propose a new conditional gradient method, based on a unified treatment of smoothing and augmented Lagrangian frameworks. The proposed method maintains favorable properties of the classical conditional gradient method, such as cheap linear minimization oracle calls and sparse representation of the decision variable. We prove O(1/√k) convergence rate for our method in the objective residual and the feasibility gap. This rate is essentially the same as the state of the art CG-type methods for our problem template, but the proposed method is arguably superior in practice compared to existing methods in various applications.</td>
</tr><tr id="Talk-OR2017" class="entry">
	<td>
	<b>[ITA2019]</b>
	Information Theory and Applications Workshop: Graduation Day<br>
	San Diego, CA, USA, January 2019.<br>
	"Sketchy decisions: Convex optimization with optimal storage (with applications to semidefinite programming)"<br>
	Joint work with: 
	Cevher, V., Fercoq, O., Locatello, F., Udell, M. and Tropp, J.A. 
	<p class="infolinks">
	[<a href="javascript:toggleInfo('Talk-OR2017','abstract')">Abstract</a>]
	 [<a href="https://drive.google.com/open?id=1lPXWTEXRzvifSGIgI_w0KXYsHDZ2rOIa" target="_blank">Slides</a>]
	
	</p>
	</td>
</tr>
<tr id="abs_Talk-OR2017" class="abstract noshow">
	<td><b>Abstract</b>: Semidefinite programs (SDP) often have low-rank solutions that can be represented with O(n)-storage, yet SDP algorithms require us to store a matrix decision variable with size (n x n). <br>This observation leads to a question central to my research: <br>Suppose that the solution to an optimization problem has a compact representation. Can we develop storage-optimal algorithms that provably approximate a solution of this problem?<br>We give an affirmative answer to this question, and develop the first storage-optimal convex optimization method for solving SDPs. We present empirical evidence of the scalability of our approach by solving convex optimization problems of trillion dimensions.</td>
</tr><tr id="Talk-ICML2018" class="entry">
	<td>
	<b>[ICML2018]</b>
	35th International Conference on Machine Learning<br>
	Stockholm, Sweden, July 2018.<br>
	"A conditional gradient framework for composite convex minimization with applications to semidefinite programming"<br>
	Joint work with: 
	Fercoq, O., Locatello, F. and Cevher, V. 
	<p class="infolinks">
	[<a href="javascript:toggleInfo('Talk-ICML2018','abstract')">Abstract</a>]
	 [<a href="https://drive.google.com/open?id=1lvpJbYyZQIThwP8oD2e1cTsA2sL-Gbdp" target="_blank">Slides</a>]
	 [<a href="https://www.youtube.com/watch?v=oXa1eZniKUE&list=PLKqPM_AcQY4xqJzWFwz7m904YwX9RiEaA&index=8&t=240s" target="_blank">Video</a>]
	</p>
	</td>
</tr>
<tr id="abs_Talk-ICML2018" class="abstract noshow">
	<td><b>Abstract</b>: We propose a conditional gradient framework for a composite convex minimization template with broad applications. Our approach combines smoothing and homotopy techniques under the CGM framework, and provably achieves the optimal $O(1/k)$ convergence rate. We demonstrate that the same rate holds if the linear subproblems are solved approximately with additive or multiplicative error. In contrast with the relevant work, we are able to characterize the convergence when the non-smooth term is an indicator function. Specific applications of our framework include the non-smooth minimization, semidefinite programming, and minimization with linear inclusion constraints over a compact domain. Numerical evidence demonstrates the benefits of our framework.</td>
</tr><tr id="Talk-ISMP2018" class="entry">
	<td>
	<b>[ISMP2018]</b>
	23rd International Symposium onMathematical Programming<br>
	Bordeaux, France, July 2018.<br>
	"A conditional gradient framework for composite convex minimization"<br>
	Joint work with: 
	Fercoq, O., Locatello, F. and Cevher, V. 
	<p class="infolinks">
	[<a href="javascript:toggleInfo('Talk-ISMP2018','abstract')">Abstract</a>]
	 [<a href="https://drive.google.com/open?id=1OPhfGXFmHbrdxpaPP4X8To3MVVhKoU3T" target="_blank">Slides</a>]
	
	</p>
	</td>
</tr>
<tr id="abs_Talk-ISMP2018" class="abstract noshow">
	<td><b>Abstract</b>: We propose a conditional gradient framework for a composite convex minimization template with broad applications. Our approach combines the notions of smoothing and homotopy under the CGM framework, and provably achieves the optimal convergence rate. We demonstrate that the same rate holds if the linear subproblems are solved approximately with additive or multiplicative error. Specific applications of the framework include the non-smooth minimization, semidefinite programming, minimization with linear inclusion constraints over a compact domain. We provide numerical evidence to demonstrate the scalability benefits of the new framework.</td>
</tr><tr id="Talk-AISTATS2017" class="entry">
	<td>
	<b>[AISTATS2017]</b>
	20th International Conference on Artificial Intelligence and Statistics<br>
	Fort Lauderdale, Florida, USA, April 2017.<br>
	"Sketchy decisions: Convex optimization with optimal storage."<br>
	Joint work with: 
	Tropp, J.A., Udell, M. and Cevher, V. 
	<p class="infolinks">
	[<a href="javascript:toggleInfo('Talk-AISTATS2017','abstract')">Abstract</a>]
	 [<a href="https://drive.google.com/open?id=1lPXWTEXRzvifSGIgI_w0KXYsHDZ2rOIa" target="_blank">Slides</a>]
	
	</p>
	</td>
</tr>
<tr id="abs_Talk-AISTATS2017" class="abstract noshow">
	<td><b>Abstract</b>: This paper concerns a fundamental class of convex matrix optimization problems. It presents the first algorithm that uses optimal storage and provably computes a lowrank approximation of a solution. In particular, when all solutions have low rank, the algorithm converges to a solution. This algorithm, SketchyCGM, modifies a standard convex optimization scheme, the conditional gradient method, to store only a small randomized sketch of the matrix variable. After the optimization terminates, the algorithm extracts a low-rank approximation of the solution from the sketch. In contrast to nonconvex heuristics, the guarantees for SketchyCGM do not rely on statistical models for the problem data. Numerical work demonstrates the benefits of SketchyCGM over heuristics.</td>
</tr><tr id="Talk-OP17" class="entry">
	<td>
	<b>[OP17]</b>
	SIAM Conference on Optimization<br>
	Vancouver, British Columbia, Canada, May 2017.<br>
	"Stochastic forward Douglas-Rachford splitting for monotone inclusions"<br>
	Joint work with: 
	Vu, B.C. and Cevher, V. 
	<p class="infolinks">
	[<a href="javascript:toggleInfo('Talk-OP17','abstract')">Abstract</a>]
	 [<a href="https://drive.google.com/open?id=1uVjpRRPaGcD1JAqG7vxW6YLPq9yD0AIa" target="_blank">Slides</a>]
	
	</p>
	</td>
</tr>
<tr id="abs_Talk-OP17" class="abstract noshow">
	<td><b>Abstract</b>: We propose a stochastic Forward-Douglas-Rachford Splitting framework for finding a zero point of the sum of three maximally monotone operators in real separable Hilbert space, where one of the operators is cocoercive. We characterize the rate of convergence in expectation in the case of strongly monotone operators. We provide guidance on step-size sequences that achieve this rate, even if the strong convexity parameter is unknown.</td>
</tr><tr id="Talk-OR2017" class="entry">
	<td>
	<b>[OR2017]</b>
	15th Swiss Operations Research Days<br>
	Fribourg, Switzerland, June 2017.<br>
	"Sketchy decisions: Convex optimization with optimal storage"<br>
	Joint work with: 
	Tropp, J.A., Udell, M. and Cevher, V. 
	<p class="infolinks">
	[<a href="javascript:toggleInfo('Talk-OR2017','abstract')">Abstract</a>]
	 [<a href="https://drive.google.com/open?id=1ZKIwWPqhQ_ZdWXvVNO6ArHVPkkaFCjRX" target="_blank">Slides</a>]
	
	</p>
	</td>
</tr>
<tr id="abs_Talk-OR2017" class="abstract noshow">
	<td><b>Abstract</b>: This paper concerns a fundamental class of convex matrix optimization problems. It presents the first algorithm that uses optimal storage and provably computes a lowrank approximation of a solution. In particular, when all solutions have low rank, the algorithm converges to a solution. This algorithm, SketchyCGM, modifies a standard convex optimization scheme, the conditional gradient method, to store only a small randomized sketch of the matrix variable. After the optimization terminates, the algorithm extracts a low-rank approximation of the solution from the sketch. In contrast to nonconvex heuristics, the guarantees for SketchyCGM do not rely on statistical models for the problem data. Numerical work demonstrates the benefits of SketchyCGM over heuristics</td>
</tr><tr id="Talk-SPARS2017" class="entry">
	<td>
	<b>[SPARS2017]</b>
	Signal Processing with Adaptive Sparse Structured Representations<br>
	Fribourg, Switzerland, June 2017.<br>
	"Scalable convex methods for low-rank matrix problems"<br>
	Joint work with: 
	Tran-Dinh, Q., Tropp, J.A., Udell, M. and Cevher, V. 
	<p class="infolinks">
	[<a href="javascript:toggleInfo('Talk-SPARS2017','abstract')">Abstract</a>]
	 [<a href="https://drive.google.com/open?id=1k2Abw5sdlAfJ9mVljftyjEE0hgGhOD1X" target="_blank">Slides</a>]
	
	</p>
	</td>
</tr>
<tr id="abs_Talk-SPARS2017" class="abstract noshow">
	<td><b>Abstract</b>: We describe our new scalable primal-dual convex optimization framework to solve low-rank matrix recovery problems. The main characteristics of our framework is the cheap per-iteration complexity and the low-memory footprint. We demonstrate the flexibility and scalability of our framework by solving matrix completion, quantum tomography and phase retrieval problems.</td>
</tr><tr id="Talk-EcoCloud2016" class="entry">
	<td>
	<b>[EcoCloud2016]</b>
	Annual Event<br>
	Lausanne, Switzerland, May 2016.<br>
	"A universal primal-dual convex optimization framework"<br>
	Joint work with: 
	Tran-Dinh, Q. and Cevher, V. 
	<p class="infolinks">
	[<a href="javascript:toggleInfo('Talk-EcoCloud2016','abstract')">Abstract</a>]
	 [<a href="https://drive.google.com/open?id=1ccWn29U292PX-P-qeVqEkjoPfeZTF7-q" target="_blank">Slides</a>]
	
	</p>
	</td>
</tr>
<tr id="abs_Talk-EcoCloud2016" class="abstract noshow">
	<td><b>Abstract</b>: We propose a new primal-dual algorithmic framework for a prototypical constrained convex optimization template. The algorithmic instances of our framework are universal since they can automatically adapt to the unknown Holder continuity degree and constant within the dual formulation. They are also guaranteed to have optimal convergence rates in the objective residual and the feasibility gap for each Holder smoothness degree. In contrast to existing primaldual algorithms, our framework avoids the proximity operator of the objective function. We instead leverage computationally cheaper, Fenchel-type operators, which are the main workhorses of the generalized conditional gradient (GCG)-type methods. In contrast to the GCG-type methods, our framework does not require the objective function to be differentiable, and can also process additional general linear inclusion constraints, while guarantees the convergence rate on the primal problem.</td>
</tr><tr id="Talk-ICCOPT2016" class="entry">
	<td>
	<b>[ICCOPT2016]</b>
	5th International Conference on Continuous Optimization<br>
	Tokyo, Japan, August 2016.<br>
	"A universal primal-dual convex optimization framework"<br>
	Joint work with: 
	Tran-Dinh, Q. and Cevher, V. 
	<p class="infolinks">
	[<a href="javascript:toggleInfo('Talk-ICCOPT2016','abstract')">Abstract</a>]
	 [<a href="https://drive.google.com/open?id=1aMjPCUiTDjjS_eH3ZdsUGb617MSntu09" target="_blank">Slides</a>]
	
	</p>
	</td>
</tr>
<tr id="abs_Talk-ICCOPT2016" class="abstract noshow">
	<td><b>Abstract</b>: We propose a new primal-dual algorithmic framework for a prototypical constrained convex optimization template. The algorithmic instances of our framework are universal since they can automatically adapt to the unknown Holder continuity degree and constant within the dual formulation. They are also guaranteed to have optimal convergence rates in the objective residual and the feasibility gap for each Holder smoothness degree. In contrast to existing primaldual algorithms, our framework avoids the proximity operator of the objective function. We instead leverage computationally cheaper, Fenchel-type operators, which are the main workhorses of the generalized conditional gradient (GCG)-type methods. In contrast to the GCG-type methods, our framework does not require the objective function to be differentiable, and can also process additional general linear inclusion constraints, while guarantees the convergence rate on the primal problem.</td>
</tr><tr id="Talk-ISMP2015" class="entry">
	<td>
	<b>[ISMP2015]</b>
	22nd International Symposium onMathematical Programming<br>
	Pittsburgh, USA, July 2015.<br>
	"Universal primal-dual proximal-gradient methods"<br>
	Joint work with: 
	Tran-Dinh, Q. and Cevher, V. 
	<p class="infolinks">
	[<a href="javascript:toggleInfo('Talk-ISMP2015','abstract')">Abstract</a>]
	 [<a href="https://drive.google.com/open?id=1MSDX8xtmXBgJEU6xMpL0jDm38HyV7Yeg" target="_blank">Slides</a>]
	
	</p>
	</td>
</tr>
<tr id="abs_Talk-ISMP2015" class="abstract noshow">
	<td><b>Abstract</b>: We propose a primal-dual algorithmic framework for a prototypical constrained convex minimization problem. The new framework aims to trade-off the computational difficulty between the primal and the dual subproblems. We achieve this in our setting by replacing the standard proximal mapping computations with linear minimization oracles in the primal space, which has been the hallmark of the scalable Frank-Wolfe type algorithms. Our analysis extends Nesterov’s universal gradient methods to the primal-dual setting in a nontrivial fashion, and provides optimal convergence guarantees for the objective residual as well as the feasibility gap without having to know the smoothness structures of the problem.</td>
</tr></tbody>
</table>
<footer>
<small>Created by <a href="http://jabref.sourceforge.net">JabRef</a> on 24/09/2020.</small>
</footer>
<!-- file generated by JabRef -->
</body>
