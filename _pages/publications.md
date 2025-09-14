---
title: My Publications
permalink: /publications/
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
th, td { border: none; padding: 0.5em; vertical-align: top; text-align: justify; }

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
<tr id="Dadras2025-pFLMF" class="entry">
	<td>Dadras, A., Stich, S.U. and Yurtsever, A. 
	(2025), 
	<i>"Personalized Federated Learning via Low-Rank Matrix Optimization"</i>
	, Transactions on Machine Learning Research.
	
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Dadras2025-pFLMF','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Dadras2025-pFLMF','bibtex')">BibTeX</a>]
	
	 [<a href="https://openreview.net/pdf?id=DFJu1QB2Nr" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Dadras2025-pFLMF" class="abstract noshow">
	<td><b>Abstract</b>: Semidefinite programming (SDP) is a powerful framework from convex optimization that has striking potential for data science applications. This paper develops a provably correct algorithm for solving large SDP problems by economizing on both the storage and the arithmetic costs. Numerical evidence shows that the method is effective for a range of applications, including relaxations of MaxCut, abstract phase retrieval, and quadratic assignment. Running on a laptop, the algorithm can handle SDP instances where the matrix variable has over 10^13 entries.</td>
</tr>
<tr id="bib_Dadras2025-pFLMF" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@article{Dadras2025-pFLMF,
  author = {Dadras, A. and Stich, S. U. and Yurtsever, A.},
  title = {Personalized Federated Learning via Low-Rank Matrix Optimization},
  journal = {Transactions on Machine Learning Research},
  year = {2025},
  url = {https://openreview.net/pdf?id=DFJu1QB2Nr}
}
</pre></td>
</tr>
<tr id="Prakhya2025-SDPNN" class="entry">
	<td>Prakhya, K., Birdal, T. and Yurtsever, A. 
	(2025), 
	<i>"Convex Formulations for Training Two-Layer ReLU Neural Networks"</i>
	
	, In 13th Int. Conf. Learning Representations (ICLR).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Prakhya2025-SDPNN','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Prakhya2025-SDPNN','bibtex')">BibTeX</a>]
	
	 [<a href="https://openreview.net/pdf?id=e0X9l4kecx" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Prakhya2025-SDPNN" class="abstract noshow">
	<td><b>Abstract</b>: We propose a class of variance-reduced stochastic conditional gradient methods. By adopting the recent stochastic path-integrated differential estimator technique (SPIDER) of Fang et. al. (2018) for the classical Frank-Wolfe (FW) method, we introduce SPIDER-FW for finite-sum minimization as well as the more general expectation minimization problems. SPIDER-FW enjoys superior complexity guarantees in the non-convex setting, while matching the best known FW variants in the convex case. We also extend our framework a la conditional gradient sliding (CGS) of Lan &amp; Zhou. (2016), and propose SPIDER-CGS.</td>
</tr>
<tr id="bib_Prakhya2025-SDPNN" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Prakhya2025-SDPNN,
  author = {Prakhya, K. and Birdal, T. and Yurtsever, A.},
  title = {Convex Formulations for Training Two-Layer ReLU Neural Networks},
  booktitle = {13th Int. Conf. Learning Representations (ICLR)},
  year = {2025},
  url = {https://openreview.net/pdf?id=e0X9l4kecx}
}
</pre></td>
</tr>
<tr id="Palenzuela2025-ProvableReduction" class="entry">
	<td>Palenzuela, K., Dadras, A., Yurtsever, A. and Lofstedt, T. 
	(2025), 
	<i>"Provable Reduction in Communication Rounds for Non-Smooth Convex Federated Learning"</i>
	
	, In 35th IEEE International Workshop on Machine Learning for Signal Processing (MLSP).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Palenzuela2025-ProvableReduction','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Palenzuela2025-ProvableReduction','bibtex')">BibTeX</a>]
	
	 [<a href="https://arxiv.org/pdf/2503.21627?" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Palenzuela2025-ProvableReduction" class="abstract noshow">
	<td><b>Abstract</b>: This paper describes scalable convex optimization methods for phase retrieval. The main characteristics of these methods are the cheap per-iteration complexity and the low-memory footprint. With a variant of the original PhaseLift formulation, we first illustrate how to leverage the scalable Frank-Wolfe (FW) method (also known as the conditional gradient algorithm), which requires a tuning parameter. We demonstrate that we can estimate the tuning parameter of the FW algorithm directly from the measurements, with rigorous theoretical guarantees. We then illustrate numerically that recent advances in universal primal-dual convex optimization methods offer significant scalability improvements over the FW method, by recovering full HD resolution color images from their quadratic measurements.</td>
</tr>
<tr id="bib_Palenzuela2025-ProvableReduction" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Palenzuela2025-ProvableReduction,
  author = {Palenzuela, K. and Dadras, A. and Yurtsever, A. and Lofstedt, T.},
  title = {Provable Reduction in Communication Rounds for Non-Smooth Convex Federated Learning},
  booktitle = {35th IEEE International Workshop on Machine Learning for Signal Processing (MLSP)},
  year = {2025},
  url = {https://arxiv.org/pdf/2503.21627?}
}
</pre></td>
</tr>
<tr id="Maskan2025-UnifiedModel" class="entry">
	<td>Maskan, H., Zygalakis, K., Eftekhari, A. and Yurtsever, A. 
	(2025), 
	<i>"A Unified Model for High-Resolution ODEs: New Insights on Accelerated Methods"</i>
	
	
	
	. arXiv preprint arXiv:2503.15136.
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Maskan2025-UnifiedModel','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Maskan2025-UnifiedModel','bibtex')">BibTeX</a>]
	
	 [<a href="https://arxiv.org/pdf/2503.15136" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Maskan2025-UnifiedModel" class="abstract noshow">
	<td><b>Abstract</b>: This paper develops a suite of algorithms for constructing low-rank approximations of an input matrix from a random linear image of the matrix, called a sketch. These methods can preserve structural properties of the input matrix, such as positive-semidefiniteness, and they can produce approximations with a user-specified rank. The algorithms are simple, accurate, numerically stable, and provably correct. Moreover, each method is accompanied by an informative error bound that allows users to select parameters a priori to achieve a given approximation quality. These claims are supported by computer experiments.</td>
</tr>
<tr id="bib_Maskan2025-UnifiedModel" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@techreport{Maskan2025-UnifiedModel,
  author = {Maskan, H. and Zygalakis, K. and Eftekhari, A. and Yurtsever, A.},
  title = {A Unified Model for High-Resolution ODEs: New Insights on Accelerated Methods},
  school = {arXiv preprint arXiv:2503.15136},
  year = {2025},
  url = {https://arxiv.org/pdf/2503.15136}
}
</pre></td>
</tr>
<tr id="Maskan2025-RevisitingNonconvexFW" class="entry">
	<td>Maskan, H., Hou, Y., Sra, S. and Yurtsever, A. 
	(2025), 
	<i>"Revisiting Frank-Wolfe for Structured Nonconvex Optimization"</i>
	
	
	
	. arXiv preprint arXiv:2503.08921.
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Maskan2025-RevisitingNonconvexFW','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Maskan2025-RevisitingNonconvexFW','bibtex')">BibTeX</a>]
	
	 [<a href="https://arxiv.org/pdf/2503.08921" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Maskan2025-RevisitingNonconvexFW" class="abstract noshow">
	<td><b>Abstract</b>: This paper develops a suite of algorithms for constructing low-rank approximations of an input matrix from a random linear image of the matrix, called a sketch. These methods can preserve structural properties of the input matrix, such as positive-semidefiniteness, and they can produce approximations with a user-specified rank. The algorithms are simple, accurate, numerically stable, and provably correct. Moreover, each method is accompanied by an informative error bound that allows users to select parameters a priori to achieve a given approximation quality. These claims are supported by computer experiments.</td>
</tr>
<tr id="bib_Maskan2025-RevisitingNonconvexFW" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@techreport{Maskan2025-RevisitingNonconvexFW,
  author = {Maskan, H. and Hou, Y. and Sra, S. and Yurtsever, A.},
  title = {Revisiting Frank-Wolfe for Structured Nonconvex Optimization},
  school = {arXiv preprint arXiv:2503.08921},
  year = {2025},
  url = {https://arxiv.org/pdf/2503.08921}
}
</pre></td>
</tr>
<tr id="Hou2025-UDV" class="entry">
	<td>Hou, Y., Sra, S. and Yurtsever, A. 
	(2025), 
	<i>"Implicit Bias in Matrix Factorization and its Explicit Realization in a New Architecture"</i>
	
	
	
	. arXiv preprint arXiv:2501.16322.
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Hou2025-UDV','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Hou2025-UDV','bibtex')">BibTeX</a>]
	
	 [<a href="https://arxiv.org/pdf/2501.16322" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Hou2025-UDV" class="abstract noshow">
	<td><b>Abstract</b>: This paper develops a suite of algorithms for constructing low-rank approximations of an input matrix from a random linear image of the matrix, called a sketch. These methods can preserve structural properties of the input matrix, such as positive-semidefiniteness, and they can produce approximations with a user-specified rank. The algorithms are simple, accurate, numerically stable, and provably correct. Moreover, each method is accompanied by an informative error bound that allows users to select parameters a priori to achieve a given approximation quality. These claims are supported by computer experiments.</td>
</tr>
<tr id="bib_Hou2025-UDV" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@techreport{Hou2025-UDV,
  author = {Hou, Y. and Sra, S. and Yurtsever, A.},
  title = {Implicit Bias in Matrix Factorization and its Explicit Realization in a New Architecture},
  school = {arXiv preprint arXiv:2501.16322},
  year = {2025},
  url = {https://arxiv.org/pdf/2501.16322}
}
</pre></td>
</tr>
<tr id="Maskan2025-BlockCoordinateDC" class="entry">
	<td>Maskan, H., Halvachi, P., Sra, S. and Yurtsever, A. 
	(2025), 
	<i>"Block Coordinate DC Programming"</i>
	
	
	
	. arXiv preprint arXiv:2411.11664.
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Maskan2025-BlockCoordinateDC','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Maskan2025-BlockCoordinateDC','bibtex')">BibTeX</a>]
	
	 [<a href="https://arxiv.org/pdf/2411.11664" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Maskan2025-BlockCoordinateDC" class="abstract noshow">
	<td><b>Abstract</b>: This paper develops a suite of algorithms for constructing low-rank approximations of an input matrix from a random linear image of the matrix, called a sketch. These methods can preserve structural properties of the input matrix, such as positive-semidefiniteness, and they can produce approximations with a user-specified rank. The algorithms are simple, accurate, numerically stable, and provably correct. Moreover, each method is accompanied by an informative error bound that allows users to select parameters a priori to achieve a given approximation quality. These claims are supported by computer experiments.</td>
</tr>
<tr id="bib_Maskan2025-BlockCoordinateDC" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@techreport{Maskan2025-BlockCoordinateDC,
  author = {Maskan, H. and Halvachi, P. and Sra, S. and Yurtsever, A.},
  title = {Block Coordinate DC Programming},
  school = {arXiv preprint arXiv:2411.11664},
  year = {2025},
  url = {https://arxiv.org/pdf/2411.11664}
}
</pre></td>
</tr>
<tr id="Dadras2024-FederatedFrankWolfe" class="entry">
	<td>Dadras, A., Banerjee, S., Prakhya, K. and Yurtsever, A. 
	(2024), 
	<i>"Federated Frank-Wolfe algorithm"</i>
	
	, In Joint European Conference on Machine Learning and Knowledge Discovery in Databases (ECML PKDD).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Dadras2024-FederatedFrankWolfe','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Dadras2024-FederatedFrankWolfe','bibtex')">BibTeX</a>]
	
	 [<a href="https://arxiv.org/pdf/2408.10090" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Dadras2024-FederatedFrankWolfe" class="abstract noshow">
	<td><b>Abstract</b>: This paper concerns a fundamental class of convex matrix optimization problems. It presents the first algorithm that uses optimal storage and provably computes a lowrank approximation of a solution. In particular, when all solutions have low rank, the algorithm converges to a solution. This algorithm, SketchyCGM, modifies a standard convex optimization scheme, the conditional gradient method, to store only a small randomized sketch of the matrix variable. After the optimization terminates, the algorithm extracts a low-rank approximation of the solution from the sketch. In contrast to nonconvex heuristics, the guarantees for SketchyCGM do not rely on statistical models for the problem data. Numerical work demonstrates the benefits of SketchyCGM over heuristics.</td>
</tr>
<tr id="bib_Dadras2024-FederatedFrankWolfe" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Dadras2024-FederatedFrankWolfe,
  author = {Dadras, A. and Banerjee, S. and Prakhya, K. and Yurtsever, A.},
  title = {Federated Frank-Wolfe algorithm},
  booktitle = {Joint European Conference on Machine Learning and Knowledge Discovery in Databases (ECML PKDD)},
  year = {2024},
  url = {https://arxiv.org/pdf/2408.10090}
}
</pre></td>
</tr>
<tr id="Banerjee2024-PersonalizedMultiTier" class="entry">
	<td>Banerjee, S., Dadras, A., Yurtsever, A. and Bhuyan, M. 
	(2024), 
	<i>"Faster One-Sample Stochastic Conditional Gradient Method for Composite Convex Minimization"</i>
	
	, In 31st International Conference on Neural Information Processing (ICONIP).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Banerjee2024-PersonalizedMultiTier','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Banerjee2024-PersonalizedMultiTier','bibtex')">BibTeX</a>]
	
	 [<a href="https://arxiv.org/pdf/2407.14251" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Banerjee2024-PersonalizedMultiTier" class="abstract noshow">
	<td><b>Abstract</b>: This paper concerns a fundamental class of convex matrix optimization problems. It presents the first algorithm that uses optimal storage and provably computes a lowrank approximation of a solution. In particular, when all solutions have low rank, the algorithm converges to a solution. This algorithm, SketchyCGM, modifies a standard convex optimization scheme, the conditional gradient method, to store only a small randomized sketch of the matrix variable. After the optimization terminates, the algorithm extracts a low-rank approximation of the solution from the sketch. In contrast to nonconvex heuristics, the guarantees for SketchyCGM do not rely on statistical models for the problem data. Numerical work demonstrates the benefits of SketchyCGM over heuristics.</td>
</tr>
<tr id="bib_Banerjee2024-PersonalizedMultiTier" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Banerjee2024-PersonalizedMultiTier,
  author = {Banerjee, S. and Dadras, A. and Yurtsever, A. and Bhuyan, M.},
  title = {Faster One-Sample Stochastic Conditional Gradient Method for Composite Convex Minimization},
  booktitle = {31st International Conference on Neural Information Processing (ICONIP)},
  year = {2024},
  url = {https://arxiv.org/pdf/2407.14251}
}
</pre></td>
</tr>
<tr id="Maskan2023-VariationalPerspective" class="entry">
	<td>Maskan, H., Zygalakis, K. and Yurtsever, A. 
	(2023), 
	<i>"A variational perspective on high-resolution ODEs"</i>
	
	, In Advances in Neural Information Processing Systems 36 (NeurIPS).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Maskan2023-VariationalPerspective','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Maskan2023-VariationalPerspective','bibtex')">BibTeX</a>]
	
	 [<a href="https://proceedings.neurips.cc/paper_files/paper/2023/file/0569458210c88d8db2985799da830d27-Paper-Conference.pdf" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Maskan2023-VariationalPerspective" class="abstract noshow">
	<td><b>Abstract</b>: Several important applications, such as streaming PCA and semidefinite programming, involve a large-scale positive-semidefinite (psd) matrix that is presented as a sequence of linear updates. Because of storage limitations, it may only be possible to retain a sketch of the psd matrix. This paper develops a new algorithm for fixed-rank psd approximation from a sketch. The approach combines the Nystr&ouml;m<br>approximation with a novel mechanism for rank truncation. Theoretical analysis establishes that the proposed method can achieve any prescribed relative error in the Schatten 1-norm and that it exploits the spectral decay of the input matrix. Computer experiments show that the proposed method dominates alternative techniques for fixed-rank psd matrix approximation across a wide range of examples.</td>
</tr>
<tr id="bib_Maskan2023-VariationalPerspective" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Maskan2023-VariationalPerspective,
  author = {Maskan, H. and Zygalakis, K. and Yurtsever, A.},
  title = {A variational perspective on high-resolution ODEs},
  booktitle = {Advances in Neural Information Processing Systems 36 (NeurIPS)},
  year = {2023},
  url = {https://proceedings.neurips.cc/paper_files/paper/2023/file/0569458210c88d8db2985799da830d27-Paper-Conference.pdf}
}
</pre></td>
</tr>
<tr id="Yurtsever2022-CCCPisFW" class="entry">
	<td>Yurtsever, A. and Sra, S. 
	(2022), 
	<i>"CCCP is Frank-Wolfe in disguise"</i>
	
	, In Advances in Neural Information Processing Systems 35 (NeurIPS).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Yurtsever2022-CCCPisFW','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Yurtsever2022-CCCPisFW','bibtex')">BibTeX</a>]
	
	 [<a href="https://proceedings.neurips.cc/paper_files/paper/2022/file/e589022774244df75b97eae18bb3628d-Paper-Conference.pdf" target="_blank">PDF</a>]
	 [<a href="https://drive.google.com/" target="_blank">Poster</a>]</p>
	</td>
</tr>
<tr id="abs_Yurtsever2022-CCCPisFW" class="abstract noshow">
	<td><b>Abstract</b>: Several important applications, such as streaming PCA and semidefinite programming, involve a large-scale positive-semidefinite (psd) matrix that is presented as a sequence of linear updates. Because of storage limitations, it may only be possible to retain a sketch of the psd matrix. This paper develops a new algorithm for fixed-rank psd approximation from a sketch. The approach combines the Nystr&ouml;m<br>approximation with a novel mechanism for rank truncation. Theoretical analysis establishes that the proposed method can achieve any prescribed relative error in the Schatten 1-norm and that it exploits the spectral decay of the input matrix. Computer experiments show that the proposed method dominates alternative techniques for fixed-rank psd matrix approximation across a wide range of examples.</td>
</tr>
<tr id="bib_Yurtsever2022-CCCPisFW" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Yurtsever2022-CCCPisFW,
  author = {Yurtsever, A. and Sra, S.},
  title = {CCCP is Frank-Wolfe in disguise},
  booktitle = {Advances in Neural Information Processing Systems 35 (NeurIPS)},
  year = {2022},
  url = {https://proceedings.neurips.cc/paper_files/paper/2022/file/e589022774244df75b97eae18bb3628d-Paper-Conference.pdf}
}
</pre></td>
</tr>
<tr id="Yurtsever2022-QuantumFrankWolfe" class="entry">
	<td>Yurtsever, A., Birdal, T. and Golyanik, V. 
	(2022), 
	<i>"Q-FW: A hybrid classical-quantum Frank-Wolfe for quadratic binary optimization"</i>
	
	, In European Conference on Computer Vision (ECCV).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Yurtsever2022-QuantumFrankWolfe','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Yurtsever2022-QuantumFrankWolfe','bibtex')">BibTeX</a>]
	
	 [<a href="https://arxiv.org/pdf/2203.12633" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Yurtsever2022-QuantumFrankWolfe" class="abstract noshow">
	<td><b>Abstract</b>: This paper concerns a fundamental class of convex matrix optimization problems. It presents the first algorithm that uses optimal storage and provably computes a lowrank approximation of a solution. In particular, when all solutions have low rank, the algorithm converges to a solution. This algorithm, SketchyCGM, modifies a standard convex optimization scheme, the conditional gradient method, to store only a small randomized sketch of the matrix variable. After the optimization terminates, the algorithm extracts a low-rank approximation of the solution from the sketch. In contrast to nonconvex heuristics, the guarantees for SketchyCGM do not rely on statistical models for the problem data. Numerical work demonstrates the benefits of SketchyCGM over heuristics.</td>
</tr>
<tr id="bib_Yurtsever2022-QuantumFrankWolfe" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Yurtsever2022-QuantumFrankWolfe,
  author = {Yurtsever, A. and Birdal, T. and Golyanik, V.},
  title = {Q-FW: A hybrid classical-quantum Frank-Wolfe for quadratic binary optimization},
  booktitle = {European Conference on Computer Vision (ECCV)},
  year = {2022},
  url = {https://arxiv.org/pdf/2203.12633}
}
</pre></td>
</tr>
<tr id="Dresdner2022-FasterOneSample" class="entry">
	<td>Dresdner, G., Vladarean, M.L., R&auml;rsch, G., Locatello, F., Cevher, V. and Yurtsever, A. 
	(2022), 
	<i>"Faster One-Sample Stochastic Conditional Gradient Method for Composite Convex Minimization"</i>
	
	, In Proc. 25th Int. Conf. Artificial Intelligence and Statistics (AISTATS).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Dresdner2022-FasterOneSample','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Dresdner2022-FasterOneSample','bibtex')">BibTeX</a>]
	
	 [<a href="https://proceedings.mlr.press/v151/dresdner22a/dresdner22a.pdf" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Dresdner2022-FasterOneSample" class="abstract noshow">
	<td><b>Abstract</b>: This paper concerns a fundamental class of convex matrix optimization problems. It presents the first algorithm that uses optimal storage and provably computes a lowrank approximation of a solution. In particular, when all solutions have low rank, the algorithm converges to a solution. This algorithm, SketchyCGM, modifies a standard convex optimization scheme, the conditional gradient method, to store only a small randomized sketch of the matrix variable. After the optimization terminates, the algorithm extracts a low-rank approximation of the solution from the sketch. In contrast to nonconvex heuristics, the guarantees for SketchyCGM do not rely on statistical models for the problem data. Numerical work demonstrates the benefits of SketchyCGM over heuristics.</td>
</tr>
<tr id="bib_Dresdner2022-FasterOneSample" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Dresdner2022-FasterOneSample,
  author = {Dresdner, G. and Vladarean, M. L. and R&auml;rsch, G. and Locatello, F. and Cevher, V. and Yurtsever, A.},
  title = {Faster One-Sample Stochastic Conditional Gradient Method for Composite Convex Minimization},
  booktitle = {Proc. 25th Int. Conf. Artificial Intelligence and Statistics (AISTATS)},
  year = {2022},
  url = {https://proceedings.mlr.press/v151/dresdner22a/dresdner22a.pdf}
}
</pre></td>
</tr>
<tr id="Yurtsever2021-AdaptiveTOS" class="entry">
	<td>Yurtsever, A., Gu, A. and Sra, S. 
	(2021), 
	<i>"Three Operator Splitting with Subgradients, Stochastic Gradients, and Adaptive Learning Rates"</i>
	
	, In Advances in Neural Information Processing Systems 34 (NeurIPS).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Yurtsever2021-AdaptiveTOS','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Yurtsever2021-AdaptiveTOS','bibtex')">BibTeX</a>]
	
	 [<a href="https://proceedings.neurips.cc/paper_files/paper/2021/file/a4267159aa970aa5a6542bcbb7ef575e-Paper.pdf" target="_blank">PDF</a>]
	 [<a href="https://drive.google.com/" target="_blank">Poster</a>]</p>
	</td>
</tr>
<tr id="abs_Yurtsever2021-AdaptiveTOS" class="abstract noshow">
	<td><b>Abstract</b>: Several important applications, such as streaming PCA and semidefinite programming, involve a large-scale positive-semidefinite (psd) matrix that is presented as a sequence of linear updates. Because of storage limitations, it may only be possible to retain a sketch of the psd matrix. This paper develops a new algorithm for fixed-rank psd approximation from a sketch. The approach combines the Nystr&ouml;m<br>approximation with a novel mechanism for rank truncation. Theoretical analysis establishes that the proposed method can achieve any prescribed relative error in the Schatten 1-norm and that it exploits the spectral decay of the input matrix. Computer experiments show that the proposed method dominates alternative techniques for fixed-rank psd matrix approximation across a wide range of examples.</td>
</tr>
<tr id="bib_Yurtsever2021-AdaptiveTOS" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Yurtsever2021-AdaptiveTOS,
  author = {Yurtsever, A. and Gu, A. and Sra, S.},
  title = {Three Operator Splitting with Subgradients, Stochastic Gradients, and Adaptive Learning Rates},
  booktitle = {Advances in Neural Information Processing Systems 34 (NeurIPS)},
  year = {2021},
  url = {https://proceedings.neurips.cc/paper_files/paper/2021/file/a4267159aa970aa5a6542bcbb7ef575e-Paper.pdf}
}
</pre></td>
</tr>
<tr id="Yurtsever2021-NonconvexTOS" class="entry">
	<td>Yurtsever, A., Mangalick, V. and Sra, S. 
	(2021), 
	<i>"Three Operator Splitting with a Nonconvex Loss Function"</i>
	
	, In Proc. 38th Int. Conf. Machine Learning (ICML).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Yurtsever2021-NonconvexTOS','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Yurtsever2021-NonconvexTOS','bibtex')">BibTeX</a>]
	
	 [<a href="http://proceedings.mlr.press/v139/yurtsever21a/yurtsever21a.pdf" target="_blank">PDF</a>]
	 [<a href="https://drive.google.com/" target="_blank">Poster</a>]</p>
	</td>
</tr>
<tr id="abs_Yurtsever2021-NonconvexTOS" class="abstract noshow">
	<td><b>Abstract</b>: We propose a class of variance-reduced stochastic conditional gradient methods. By adopting the recent stochastic path-integrated differential estimator technique (SPIDER) of Fang et. al. (2018) for the classical Frank-Wolfe (FW) method, we introduce SPIDER-FW for finite-sum minimization as well as the more general expectation minimization problems. SPIDER-FW enjoys superior complexity guarantees in the non-convex setting, while matching the best known FW variants in the convex case. We also extend our framework a la conditional gradient sliding (CGS) of Lan &amp; Zhou. (2016), and propose SPIDER-CGS.</td>
</tr>
<tr id="bib_Yurtsever2021-NonconvexTOS" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Yurtsever2021-NonconvexTOS,
  author = {Yurtsever, A. and Mangalick, V. and Sra, S.},
  title = {Three Operator Splitting with a Nonconvex Loss Function},
  booktitle = {Proc. 38th Int. Conf. Machine Learning (ICML)},
  year = {2021},
  url = {http://proceedings.mlr.press/v139/yurtsever21a/yurtsever21a.pdf}
}
</pre></td>
</tr>
<tr id="Yurtsever2017-ScalableLowRank" class="entry">
	<td>Yurtsever, A., Tran-Dinh, Q. and Cevher, V. 
	(2017), 
	<i>"Scalable Convex Methods for Low-Rank Matrix Problems"</i>
	
	, In Signal Processing with Adaptive Sparse Structured Representations (SPARS).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Yurtsever2017-ScalableLowRank','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Yurtsever2017-ScalableLowRank','bibtex')">BibTeX</a>]
	
	 [<a href="https://spars2017.lx.it.pt/index_files/papers/SPARS2017_Paper_11.pdf" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Yurtsever2017-ScalableLowRank" class="abstract noshow">
	<td><b>Abstract</b>: We study a phase retrieval problem in the Poisson noise model. Motivated by the PhaseLift approach, we approximate the maximumlikelihood estimator by solving a convex program with a nuclear norm constraint. While the Frank-Wolfe algorithm, together with the Lanczos method, can efficiently deal with nuclear norm constraints, our objective function does not have a Lipschitz continuous gradient, and hence existing convergence guarantees for the Frank-Wolfe algorithm do not apply. In this paper, we show that the Frank-Wolfe algorithm works for the Poisson phase retrieval problem, and has a<br>global convergence rate of O(1/t), where t is the iteration counter. We provide rigorous theoretical guarantee and illustrating numerical results.</td>
</tr>
<tr id="bib_Yurtsever2017-ScalableLowRank" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Yurtsever2017-ScalableLowRank,
  author = {Yurtsever, A. and Tran-Dinh, Q. and Cevher, V.},
  title = {Scalable Convex Methods for Low-Rank Matrix Problems},
  booktitle = {Signal Processing with Adaptive Sparse Structured Representations (SPARS)},
  year = {2017},
  url = {https://spars2017.lx.it.pt/index_files/papers/SPARS2017_Paper_11.pdf}
}
</pre></td>
</tr>
<tr id="Bang2018-NonconvexAugmentedLagrangian" class="entry">
	<td>Vu, B., Alacaoglu, A., Sahin, M., Yurtsever, A. and Cevher, V. 
	(2018), 
	<i>"A First-Order Augmented Lagrangian Framework for Nonconvex Optimization with Nonlinear Constraints"</i>
	
	, In Modern Trends in Nonconvex Optimization for Machine Learning (ICML Workshop).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Bang2018-NonconvexAugmentedLagrangian','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Bang2018-NonconvexAugmentedLagrangian','bibtex')">BibTeX</a>]
	
	 [<a href="https://drive.google.com/file/d/1r0pM67WKxqeh0ojJNaAJeq_9VKOEv4C7/preview" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Bang2018-NonconvexAugmentedLagrangian" class="abstract noshow">
	<td><b>Abstract</b>: We consider a canonical nonlinear-constrained nonconvex problem with broad applications in machine learning, theoretical computer science, and signal processing. We propose a simple primal-dual splitting scheme that provably converges to a stationary point of the non-convex problem. We achieve this desideratum via an adaptive and inexact augmented Lagrangian method. The new algorithm features a slow O(1/3) convergence rate, which it counteracted by its cheap per-iteration complexity. We provide numerical evidence on large-scale machine learning problems, modeled typically via semidefinite relaxations.</td>
</tr>
<tr id="bib_Bang2018-NonconvexAugmentedLagrangian" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Bang2018-NonconvexAugmentedLagrangian,
  author = {Vu, B.C. and Alacaoglu, A. and Sahin, M.F. and Yurtsever, A. and Cevher, V.},
  title = {A First-Order Augmented Lagrangian Framework for Nonconvex Optimization with Nonlinear Constraints},
  booktitle = {Modern Trends in Nonconvex Optimization for Machine Learning (ICML Workshop)},
  year = {2018},
  url = {https://drive.google.com/file/d/1r0pM67WKxqeh0ojJNaAJeq_9VKOEv4C7/preview}
}
</pre></td>
</tr>
<tr id="Cevher2018-StochasticFDR" class="entry">
	<td>Cevher, V., Vu, B. and Yurtsever, A. 
	(2018), 
	<i>"Stochastic Forward Douglas-Rachford Splitting Method for Monotone Inclusions"</i>
	
	, In Large-Scale and Distributed Optimization.
	
	
	
	 
	
	
	
	 Springer International Publishing.
	<p class="infolinks">[<a href="javascript:toggleInfo('Cevher2018-StochasticFDR','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Cevher2018-StochasticFDR','bibtex')">BibTeX</a>]
	
	
	</p>
	</td>
</tr>
<tr id="abs_Cevher2018-StochasticFDR" class="abstract noshow">
	<td><b>Abstract</b>: We propose a stochastic Forward-Douglas-Rachford Splitting framework for finding a zero point of the sum of three maximally monotone operators in real separable Hilbert space, where one of the operators is cocoercive. We characterize the rate of convergence in expectation in the case of strongly monotone operators. We provide guidance on step-size sequences that achieve this rate, even if the strong convexity parameter is unknown.</td>
</tr>
<tr id="bib_Cevher2018-StochasticFDR" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@incollection{Cevher2018-StochasticFDR,
  author = {Cevher, V. and Vu, B.C. and Yurtsever, A.},
  editor = {Giselsson, P. and Rantzer, A.},
  title = {Stochastic Forward Douglas-Rachford Splitting Method for Monotone Inclusions},
  booktitle = {Large-Scale and Distributed Optimization},
  publisher = {Springer International Publishing},
  year = {2018}
}
</pre></td>
</tr>
<tr id="Cevher2019-InertialTOS" class="entry">
	<td>Cevher, V., Vu, B. and Yurtsever, A. 
	(2019), 
	<i>"Inertial Three-Operator Splitting Method and Applications"</i>
	
	
	
	. arXiv:1904.12980.
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Cevher2019-InertialTOS','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Cevher2019-InertialTOS','bibtex')">BibTeX</a>]
	
	 [<a href="https://arxiv.org/pdf/1904.12980.pdf" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Cevher2019-InertialTOS" class="abstract noshow">
	<td><b>Abstract</b>: We introduce an inertial variant of the forward-Douglas-Rachford splitting and analyze its convergence. We specify an instance of the proposed method to the three-composite convex minimization template. We provide practical guidance on the selection of the inertial parameter based on the adaptive starting idea. Finally, we illustrate the practical performance of our method in various machine learning applications.</td>
</tr>
<tr id="bib_Cevher2019-InertialTOS" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@techreport{Cevher2019-InertialTOS,
  author = {Cevher, V. and B. Vu and Yurtsever, A.},
  title = {Inertial Three-Operator Splitting Method and Applications},
  school = {arXiv:1904.12980},
  year = {2019},
  url = {https://arxiv.org/pdf/1904.12980.pdf}
}
</pre></td>
</tr>
<tr id="Ding2021-ComplementarySlacknessSDP" class="entry">
	<td>Ding, L., Yurtsever, A., Cevher, V., Tropp, J. and Udell, M. 
	(2021), 
	<i>"An Optimal-Storage Approach to Semidefinite Programming using Approximate Complementarity"</i>
	, SIAM Journal on Optimization.
	
	
	. arXiv:1902.03373.
	
	 
	 Vol. 31
	(4)
	, pp. 2695-2725.
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Ding2021-ComplementarySlacknessSDP','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Ding2021-ComplementarySlacknessSDP','bibtex')">BibTeX</a>]
	
	 [<a href="https://arxiv.org/pdf/1902.03373.pdf" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Ding2021-ComplementarySlacknessSDP" class="abstract noshow">
	<td><b>Abstract</b>: This paper develops a new storage-optimal algorithm that provably solves generic semidefinite programs (SDPs) in standard form. This method is particularly effective for weakly constrained SDPs. The key idea is to formulate an approximate complementarity principle: Given an approximate solution to the dual SDP, the primal SDP has an approximate solution whose range is contained in the eigenspace with small eigenvalues of the dual slack matrix. For weakly constrained SDPs, this eigenspace has very low dimension, so this observation significantly reduces the search space for the primal solution.<br>This result suggests an algorithmic strategy that can be implemented with minimal storage:<br>(1) Solve the dual SDP approximately;<br>(2) compress the primal SDP to the eigenspace with small eigenvalues of the dual slack matrix;<br>(3) solve the compressed primal SDP.<br>The paper also provides numerical experiments showing that this approach is successful for a range of interesting large-scale SDPs.</td>
</tr>
<tr id="bib_Ding2021-ComplementarySlacknessSDP" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@article{Ding2021-ComplementarySlacknessSDP,
  author = {Ding, L. and Yurtsever, A. and Cevher, V. and Tropp, J. and Udell, M.},
  title = {An Optimal-Storage Approach to Semidefinite Programming using Approximate Complementarity},
  journal = {SIAM Journal on Optimization},
  school = {arXiv:1902.03373},
  year = {2021},
  volume = {31},
  number = {4},
  pages = {2695-2725},
  url = {https://arxiv.org/pdf/1902.03373.pdf}
}
</pre></td>
</tr>
<tr id="Hsieh2018-NonEuclideanFactorization" class="entry">
	<td>Hsieh, Y.-P., Kao, Y.-C., Mahabadi, R.K., Yurtsever, A., Kyrillidis, A. and Cevher, V. 
	(2018), 
	<i>"A Non-Euclidean Gradient Descent Framework for Non-Convex Matrix Factorization"</i>
	, IEEE Transactions on Signal Processing.
	
	
	
	
	 
	 Vol. 22
	(66)
	, pp. 5917-5926.
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Hsieh2018-NonEuclideanFactorization','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Hsieh2018-NonEuclideanFactorization','bibtex')">BibTeX</a>]
	
	 [<a href="https://infoscience.epfl.ch/record/231191/files/matrix_fact.pdf" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Hsieh2018-NonEuclideanFactorization" class="abstract noshow">
	<td><b>Abstract</b>: We study convex optimization problems that feature low-rank matrix solutions. In such scenarios, non-convex methods offer significant advantages over convex methods due to their lower space complexity, as well as practical faster convergence. Under mild assumptions, these methods feature global convergence guarantees.<br><br><br>In this paper, we extend the results on this matter by following a different path. We derive a non-Euclidean optimization framework in the non-convex setting that takes nonlinear gradient steps on the factors. Our framework enables the possibility to further exploit the underlying problem structures, such as sparsity or low-rankness on the factorized domain, or better dimensional dependence of the smoothness parameters of the objectives. We prove that the non-Euclidean methods enjoy the same rigorous guarantees as their Euclidean counterparts, under appropriate assumptions. Numerical evidence with Fourier Ptychography and FastText applications, using real data, shows that our approach can enhance solution quality, as well as convergence speed over the standard non-convex approaches.</td>
</tr>
<tr id="bib_Hsieh2018-NonEuclideanFactorization" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@article{Hsieh2018-NonEuclideanFactorization,
  author = {Y.-P. Hsieh and Y.-C. Kao and R. K. Mahabadi and Yurtsever, A. and Kyrillidis, A. and Cevher, V.},
  title = {A Non-Euclidean Gradient Descent Framework for Non-Convex Matrix Factorization},
  journal = {IEEE Transactions on Signal Processing},
  year = {2018},
  volume = {22},
  number = {66},
  pages = {5917--5926},
  url = {https://infoscience.epfl.ch/record/231191/files/matrix_fact.pdf}
}
</pre></td>
</tr>
<tr id="Levy2018-Accelegrad" class="entry">
	<td>Levy, K., Yurtsever, A. and Cevher, V. 
	(2018), 
	<i>"Online Adaptive Methods, Universality and Acceleration"</i>
	
	, In Advances in Neural Information Processing Systems 31 (NeurIPS).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Levy2018-Accelegrad','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Levy2018-Accelegrad','bibtex')">BibTeX</a>]
	
	 [<a href="https://papers.nips.cc/paper/7885-online-adaptive-methods-universality-and-acceleration.pdf" target="_blank">PDF</a>]
	 [<a href="https://drive.google.com/file/d/1LMhZg3s-MmM5X6OnI2VFP4ZAWZJLPLOy/view" target="_blank">Poster</a>]</p>
	</td>
</tr>
<tr id="abs_Levy2018-Accelegrad" class="abstract noshow">
	<td><b>Abstract</b>: We present a novel method for convex unconstrained optimization that, without any modifications, ensures: (i) accelerated convergence rate for smooth objectives, (ii) standard convergence rate in the general (non-smooth) setting, and (iii) standard convergence rate in the stochastic optimization setting. To the best of our knowledge, this is the first method that simultaneously applies to all of the above settings. <br><br>At the heart of our method is an adaptive learning rate rule that employs importance weights, in the spirit of adaptive online learning algorithms, combined with an update that linearly couples two sequences. An empirical examination of our method demonstrates its applicability to the above mentioned scnearios and corroborates our theoretical findings.</td>
</tr>
<tr id="bib_Levy2018-Accelegrad" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Levy2018-Accelegrad,
  author = {Levy, K.Y. and Yurtsever, A. and Cevher, V.},
  title = {Online Adaptive Methods, Universality and Acceleration},
  booktitle = {Advances in Neural Information Processing Systems 31 (NeurIPS)},
  year = {2018},
  url = {https://papers.nips.cc/paper/7885-online-adaptive-methods-universality-and-acceleration.pdf}
}
</pre></td>
</tr>
<tr id="Locatello2019-StochasticHomotopyCGM" class="entry">
	<td>Locatello, F., Yurtsever, A., Fercoq, O. and Cevher, V. 
	(2019), 
	<i>"Stochastic Conditional Gradient Method for Composite Convex Minimization"</i>
	
	, In Advances in Neural Information Processing Systems 32 (NeurIPS).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Locatello2019-StochasticHomotopyCGM','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Locatello2019-StochasticHomotopyCGM','bibtex')">BibTeX</a>]
	
	 [<a href="https://papers.nips.cc/paper/9572-stochastic-frank-wolfe-for-composite-convex-minimization.pdf" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Locatello2019-StochasticHomotopyCGM" class="abstract noshow">
	<td><b>Abstract</b>: A broad class of convex optimization problems can be formulated as a semidefinite program (SDP), minimization of a convex function over the positive-semidefinite cone subject to some affine constraints. The majority of classical SDP solvers are designed for the deterministic setting where problem data is readily available. In this setting, generalized conditional gradient methods (aka Frank-Wolfe-type methods) provide scalable solutions by leveraging the so-called linear minimization oracle instead of the projection onto the semidefinite cone. Most problems in machine learning and modern engineering applications, however, contain some degree of stochasticity. In this work, we propose the first conditional-gradient-type method for solving stochastic optimization problems under affine constraints. Our method guarantees O(k^−1/3) convergence rate in expectation on the objective residual and O(k^−5/12)on the feasibility gap.</td>
</tr>
<tr id="bib_Locatello2019-StochasticHomotopyCGM" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Locatello2019-StochasticHomotopyCGM,
  author = {Locatello, F. and Yurtsever, A. and Fercoq, O. and Cevher, V.},
  title = {Stochastic Conditional Gradient Method for Composite Convex Minimization},
  booktitle = {Advances in Neural Information Processing Systems 32 (NeurIPS)},
  year = {2019},
  url = {https://papers.nips.cc/paper/9572-stochastic-frank-wolfe-for-composite-convex-minimization.pdf}
}
</pre></td>
</tr>
<tr id="Odor2016-PoissonPhaseRetrieval" class="entry">
	<td>Odor, G., Li, Y.-H., Yurtsever, A., Hsieh, Y.-P., Tran-Dinh, Q., El&nbsp;Halabi, M. and Cevher, V. 
	(2016), 
	<i>"Frank-Wolfe Works for Non-Lipschitz Continuous Gradient Objectives: Scalable Poisson Phase Retrieval"</i>
	
	, In 41st IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Odor2016-PoissonPhaseRetrieval','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Odor2016-PoissonPhaseRetrieval','bibtex')">BibTeX</a>]
	
	 [<a href="https://arxiv.org/pdf/1602.00724.pdf" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Odor2016-PoissonPhaseRetrieval" class="abstract noshow">
	<td><b>Abstract</b>: We study a phase retrieval problem in the Poisson noise model. Motivated by the PhaseLift approach, we approximate the maximumlikelihood estimator by solving a convex program with a nuclear norm constraint. While the Frank-Wolfe algorithm, together with the Lanczos method, can efficiently deal with nuclear norm constraints, our objective function does not have a Lipschitz continuous gradient, and hence existing convergence guarantees for the Frank-Wolfe algorithm do not apply. In this paper, we show that the Frank-Wolfe algorithm works for the Poisson phase retrieval problem, and has a<br>global convergence rate of O(1/t), where t is the iteration counter. We provide rigorous theoretical guarantee and illustrating numerical results.</td>
</tr>
<tr id="bib_Odor2016-PoissonPhaseRetrieval" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Odor2016-PoissonPhaseRetrieval,
  author = {Odor, G. and Li, Y.-H. and Yurtsever, A. and Hsieh, Y.-P. and Tran-Dinh, Q. and El&nbsp;Halabi, M. and Cevher, V.},
  title = {Frank-Wolfe Works for Non-Lipschitz Continuous Gradient Objectives: Scalable Poisson Phase Retrieval},
  booktitle = {41st IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP)},
  year = {2016},
  url = {https://arxiv.org/pdf/1602.00724.pdf}
}
</pre></td>
</tr>
<tr id="Tropp2017-NystromSketch" class="entry">
	<td>Tropp, J., Yurtsever, A., Udell, M. and Cevher, V. 
	(2017), 
	<i>"Fixed-rank approximation of a positive-semidefinite matrix from streaming data"</i>
	
	, In Advances in Neural Information Processing Systems 31 (NeurIPS).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Tropp2017-NystromSketch','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Tropp2017-NystromSketch','bibtex')">BibTeX</a>]
	
	 [<a href="http://papers.nips.cc/paper/6722-fixed-rank-approximation-of-a-positive-semidefinite-matrix-from-streaming-data.pdf" target="_blank">PDF</a>]
	 [<a href="https://drive.google.com/file/d/1runlikLuyTjuS2IH0I3pngQKKUdxPX-g/view" target="_blank">Poster</a>]</p>
	</td>
</tr>
<tr id="abs_Tropp2017-NystromSketch" class="abstract noshow">
	<td><b>Abstract</b>: Several important applications, such as streaming PCA and semidefinite programming, involve a large-scale positive-semidefinite (psd) matrix that is presented as a sequence of linear updates. Because of storage limitations, it may only be possible to retain a sketch of the psd matrix. This paper develops a new algorithm for fixed-rank psd approximation from a sketch. The approach combines the Nystr&ouml;m<br>approximation with a novel mechanism for rank truncation. Theoretical analysis establishes that the proposed method can achieve any prescribed relative error in the Schatten 1-norm and that it exploits the spectral decay of the input matrix. Computer experiments show that the proposed method dominates alternative techniques for fixed-rank psd matrix approximation across a wide range of examples.</td>
</tr>
<tr id="bib_Tropp2017-NystromSketch" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Tropp2017-NystromSketch,
  author = {Tropp, J.A. and Yurtsever, A. and Udell, M. and Cevher, V.},
  title = {Fixed-rank approximation of a positive-semidefinite matrix from streaming data},
  booktitle = {Advances in Neural Information Processing Systems 31 (NeurIPS)},
  year = {2017},
  url = {http://papers.nips.cc/paper/6722-fixed-rank-approximation-of-a-positive-semidefinite-matrix-from-streaming-data.pdf}
}
</pre></td>
</tr>
<tr id="Tropp2017-PracticalSketch" class="entry">
	<td>Tropp, J., Yurtsever, A., Udell, M. and Cevher, V. 
	(2017), 
	<i>"Practical sketching algorithms for low-rank matrix approximation"</i>
	, SIAM Journal on Matrix Analysis and Applications.
	
	
	
	
	 
	 Vol. 38
	(4)
	, pp. 1454-1485.
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Tropp2017-PracticalSketch','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Tropp2017-PracticalSketch','bibtex')">BibTeX</a>]
	
	 [<a href="https://arxiv.org/pdf/1609.00048.pdf" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Tropp2017-PracticalSketch" class="abstract noshow">
	<td><b>Abstract</b>: This paper describes a suite of algorithms for constructing low-rank approximations of an input matrix from a random linear image of the matrix, called a sketch. These methods can preserve structural properties of the input matrix, such as positive-semidefiniteness, and they can produce approximations with a user-specified rank. The algorithms are simple, accurate, numerically stable, and provably correct. Moreover, each method is accompanied by an informative error bound that allows users to select parameters a priori to achieve a given approximation quality. These claims are supported by numerical experiments with real and synthetic data.</td>
</tr>
<tr id="bib_Tropp2017-PracticalSketch" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@article{Tropp2017-PracticalSketch,
  author = {Tropp, J.A. and Yurtsever, A. and Udell, M. and Cevher, V.},
  title = {Practical sketching algorithms for low-rank matrix approximation},
  journal = {SIAM Journal on Matrix Analysis and Applications},
  year = {2017},
  volume = {38},
  number = {4},
  pages = {1454--1485},
  url = {https://arxiv.org/pdf/1609.00048.pdf}
}
</pre></td>
</tr>
<tr id="Tropp2017-RandomizedSingleView" class="entry">
	<td>Tropp, J., Yurtsever, A., Udell, M. and Cevher, V. 
	(2017), 
	<i>"Randomized single-view algorithms for low-rank matrix approximation"</i>
	
	
	
	. ACM Report 2017-01, Caltech.
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Tropp2017-RandomizedSingleView','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Tropp2017-RandomizedSingleView','bibtex')">BibTeX</a>]
	
	 [<a href="https://authors.library.caltech.edu/74347/1/ACM_TR_2017_01.pdf" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Tropp2017-RandomizedSingleView" class="abstract noshow">
	<td><b>Abstract</b>: This paper develops a suite of algorithms for constructing low-rank approximations of an input matrix from a random linear image of the matrix, called a sketch. These methods can preserve structural properties of the input matrix, such as positive-semidefiniteness, and they can produce approximations with a user-specified rank. The algorithms are simple, accurate, numerically stable, and provably correct. Moreover, each method is accompanied by an informative error bound that allows users to select parameters a priori to achieve a given approximation quality. These claims are supported by computer experiments.</td>
</tr>
<tr id="bib_Tropp2017-RandomizedSingleView" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@techreport{Tropp2017-RandomizedSingleView,
  author = {Tropp, J.A. and Yurtsever, A. and Udell, M. and Cevher, V.},
  title = {Randomized single-view algorithms for low-rank matrix approximation},
  school = {ACM Report 2017-01, Caltech},
  year = {2017},
  url = {https://authors.library.caltech.edu/74347/1/ACM_TR_2017_01.pdf}
}
</pre></td>
</tr>
<tr id="Tropp2019-SketchingScientificSimulation" class="entry">
	<td>Tropp, J., Yurtsever, A., Udell, M. and Cevher, V. 
	(2019), 
	<i>"Streaming Low-Rank Matrix Approximation with an Application to Scientific Simulation"</i>
	, SIAM Journal on Scientific Computing.
	
	
	
	
	 
	 Vol. 41
	(4)
	, pp. A2430-A2463.
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Tropp2019-SketchingScientificSimulation','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Tropp2019-SketchingScientificSimulation','bibtex')">BibTeX</a>]
	
	 [<a href="https://arxiv.org/pdf/1902.08651.pdf" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Tropp2019-SketchingScientificSimulation" class="abstract noshow">
	<td><b>Abstract</b>: This paper argues that randomized linear sketching is a natural tool for on-the-fly compression of data matrices that arise from large-scale scientific simulations and data collection. The technical contribution consists in a new algorithm for constructing an accurate low-rank approximation of a matrix from streaming data. This method is accompanied by an a priori analysis that allows the user to set algorithm parameters with confidence and an a posteriori error estimator that allows the user to validate the quality of the reconstructed matrix. In comparison to previous techniques, the new method achieves smaller relative approximation errors and is less sensitive to parameter choices. As concrete applications, the paper outlines how the algorithm can be used to compress a Navier--Stokes simulation and a sea surface temperature dataset.</td>
</tr>
<tr id="bib_Tropp2019-SketchingScientificSimulation" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@article{Tropp2019-SketchingScientificSimulation,
  author = {Tropp, J.A. and Yurtsever, A. and Udell, M. and Cevher, V.},
  title = {Streaming Low-Rank Matrix Approximation with an Application to Scientific Simulation},
  journal = {SIAM Journal on Scientific Computing},
  year = {2019},
  volume = {41},
  number = {4},
  pages = {A2430--A2463},
  url = {https://arxiv.org/pdf/1902.08651.pdf}
}
</pre></td>
</tr>
<tr id="Yurtsever2015-ScalablePhaseRetrieval" class="entry">
	<td>Yurtsever, A., Hsieh, Y.-P. and Cevher, V. 
	(2015), 
	<i>"Scalable Convex Methods for Phase Retrieval"</i>
	
	, In 6th IEEE Int. Workshop Computational Advances in Multi-Sensor Adaptive Processing (CAMSAP).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Yurtsever2015-ScalablePhaseRetrieval','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Yurtsever2015-ScalablePhaseRetrieval','bibtex')">BibTeX</a>]
	
	 [<a href="https://infoscience.epfl.ch/record/212914/files/ScalableConvexMethodsForPhaseRetrieval.pdf" target="_blank">PDF</a>]
	 [<a href="https://drive.google.com/file/d/18OoZq4QqH2uvBxdJCGMbv7nmSFy29PWj/view" target="_blank">Poster</a>]</p>
	</td>
</tr>
<tr id="abs_Yurtsever2015-ScalablePhaseRetrieval" class="abstract noshow">
	<td><b>Abstract</b>: This paper describes scalable convex optimization methods for phase retrieval. The main characteristics of these methods are the cheap per-iteration complexity and the low-memory footprint. With a variant of the original PhaseLift formulation, we first illustrate how to leverage the scalable Frank-Wolfe (FW) method (also known as the conditional gradient algorithm), which requires a tuning parameter. We demonstrate that we can estimate the tuning parameter of the FW algorithm directly from the measurements, with rigorous theoretical guarantees. We then illustrate numerically that recent advances in universal primal-dual convex optimization methods offer significant scalability improvements over the FW method, by recovering full HD resolution color images from their quadratic measurements.</td>
</tr>
<tr id="bib_Yurtsever2015-ScalablePhaseRetrieval" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Yurtsever2015-ScalablePhaseRetrieval,
  author = {Yurtsever, A. and Hsieh, Y.-P. and Cevher, V.},
  title = {Scalable Convex Methods for Phase Retrieval},
  booktitle = {6th IEEE Int. Workshop Computational Advances in Multi-Sensor Adaptive Processing (CAMSAP)},
  year = {2015},
  url = {https://infoscience.epfl.ch/record/212914/files/ScalableConvexMethodsForPhaseRetrieval.pdf}
}
</pre></td>
</tr>
<tr id="Yurtsever2015-UniversalPrimalDual" class="entry">
	<td>Yurtsever, A., Tran-Dinh, Q. and Cevher, V. 
	(2015), 
	<i>"A Universal Primal-Dual Convex Optimization Framework"</i>
	
	, In Advances in Neural Information Processing Systems 28 (NeurIPS).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Yurtsever2015-UniversalPrimalDual','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Yurtsever2015-UniversalPrimalDual','bibtex')">BibTeX</a>]
	
	 [<a href="http://papers.nips.cc/paper/5826-a-universal-primal-dual-convex-optimization-framework.pdf" target="_blank">PDF</a>]
	 [<a href="https://drive.google.com/file/d/1ulLC1uSZD2NTTkUuCQSkYmOCdeClKQfb/view" target="_blank">Poster</a>]</p>
	</td>
</tr>
<tr id="abs_Yurtsever2015-UniversalPrimalDual" class="abstract noshow">
	<td><b>Abstract</b>: We propose a new primal-dual algorithmic framework for a prototypical constrained convex optimization template. The algorithmic instances of our framework are universal since they can automatically adapt to the unknown Holder continuity degree and constant within the dual formulation. They are also guaranteed to have optimal convergence rates in the objective residual and the feasibility gap for each Holder smoothness degree. In contrast to existing primal-dual algorithms, our framework avoids the proximity operator of the objective function. We instead leverage computationally cheaper, Fenchel-type operators, which are the main workhorses of the generalized conditional gradient (GCG)-type methods. In contrast to the GCG-type methods, our framework does not require the objective function to be differentiable, and can also process additional general linear inclusion constraints, while guarantees the convergence rate on the primal problem.</td>
</tr>
<tr id="bib_Yurtsever2015-UniversalPrimalDual" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Yurtsever2015-UniversalPrimalDual,
  author = {Yurtsever, A. and Tran-Dinh, Q. and Cevher, V.},
  title = {A Universal Primal-Dual Convex Optimization Framework},
  booktitle = {Advances in Neural Information Processing Systems 28 (NeurIPS)},
  year = {2015},
  url = {http://papers.nips.cc/paper/5826-a-universal-primal-dual-convex-optimization-framework.pdf}
}
</pre></td>
</tr>
<tr id="Yurtsever2016-SketchyDecision" class="entry">
	<td>Yurtsever, A., Udell, M., Tropp, J. and Cevher, V. 
	(2017), 
	<i>"Sketchy Decisions: Convex Low-Rank Matrix Optimization with Optimal Storage"</i>
	
	, In Proc. 20th Int. Conf. Artificial Intelligence and Statistics (AISTATS).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Yurtsever2016-SketchyDecision','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Yurtsever2016-SketchyDecision','bibtex')">BibTeX</a>]
	
	 [<a href="https://arxiv.org/pdf/1702.06838.pdf" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Yurtsever2016-SketchyDecision" class="abstract noshow">
	<td><b>Abstract</b>: This paper concerns a fundamental class of convex matrix optimization problems. It presents the first algorithm that uses optimal storage and provably computes a lowrank approximation of a solution. In particular, when all solutions have low rank, the algorithm converges to a solution. This algorithm, SketchyCGM, modifies a standard convex optimization scheme, the conditional gradient method, to store only a small randomized sketch of the matrix variable. After the optimization terminates, the algorithm extracts a low-rank approximation of the solution from the sketch. In contrast to nonconvex heuristics, the guarantees for SketchyCGM do not rely on statistical models for the problem data. Numerical work demonstrates the benefits of SketchyCGM over heuristics.</td>
</tr>
<tr id="bib_Yurtsever2016-SketchyDecision" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Yurtsever2016-SketchyDecision,
  author = {Yurtsever, A. and Udell, M. and Tropp, J.A. and Cevher, V.},
  title = {Sketchy Decisions: Convex Low-Rank Matrix Optimization with Optimal Storage},
  booktitle = {Proc. 20th Int. Conf. Artificial Intelligence and Statistics (AISTATS)},
  year = {2017},
  url = {https://arxiv.org/pdf/1702.06838.pdf}
}
</pre></td>
</tr>
<tr id="Yurtsever2016-StochasticThreeOperator" class="entry">
	<td>Yurtsever, A., Vu, B. and Cevher, V. 
	(2016), 
	<i>"Stochastic Three-Composite Convex Minimization"</i>
	
	, In Advances in Neural Information Processing Systems 29 (NeurIPS).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Yurtsever2016-StochasticThreeOperator','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Yurtsever2016-StochasticThreeOperator','bibtex')">BibTeX</a>]
	
	 [<a href="http://papers.nips.cc/paper/6127-stochastic-three-composite-convex-minimization.pdf" target="_blank">PDF</a>]
	 [<a href="https://drive.google.com/file/d/1BjAq0ZVgxENQEErnLp7Bo2pyP1Zgoofn/view" target="_blank">Poster</a>]</p>
	</td>
</tr>
<tr id="abs_Yurtsever2016-StochasticThreeOperator" class="abstract noshow">
	<td><b>Abstract</b>: We propose a stochastic optimization method for the minimization of the sum of three convex functions, one of which has Lipschitz continuous gradient as well as restricted strong convexity. Our approach is most suitable in the setting where it is computationally advantageous to process smooth term in the decomposition with its stochastic gradient estimate and the other two functions separately with their proximal operators, such as doubly regularized empirical risk minimization problems. We prove the convergence characterization of the proposed algorithm in expectation under the standard assumptions for the stochastic gradient estimate of the smooth term. Our method operates in the primal space and can be considered as a stochastic extension of the three-operator splitting method. Numerical evidence supports the effectiveness of our method in real-world problems.</td>
</tr>
<tr id="bib_Yurtsever2016-StochasticThreeOperator" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Yurtsever2016-StochasticThreeOperator,
  author = {Yurtsever, A. and Vu, B.C. and Cevher, V.},
  title = {Stochastic Three-Composite Convex Minimization},
  booktitle = {Advances in Neural Information Processing Systems 29 (NeurIPS)},
  year = {2016},
  url = {http://papers.nips.cc/paper/6127-stochastic-three-composite-convex-minimization.pdf}
}
</pre></td>
</tr>
<tr id="Yurtsever2018-HomotopyCGM" class="entry">
	<td>Yurtsever, A., Fercoq, O., Locatello, F. and Cevher, V. 
	(2018), 
	<i>"A Conditional Gradient Framework for Composite Convex Minimization with Applications to Semidefinite Programming"</i>
	
	, In Proc. 35th Int. Conf. Machine Learning (ICML).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Yurtsever2018-HomotopyCGM','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Yurtsever2018-HomotopyCGM','bibtex')">BibTeX</a>]
	
	 [<a href="http://proceedings.mlr.press/v80/yurtsever18a/yurtsever18a.pdf" target="_blank">PDF</a>]
	 [<a href="https://drive.google.com/file/d/1rTTxlH2GYph7Y7sNQvvKl2KOgluH8mJi/view" target="_blank">Poster</a>]</p>
	</td>
</tr>
<tr id="abs_Yurtsever2018-HomotopyCGM" class="abstract noshow">
	<td><b>Abstract</b>: We propose a conditional gradient framework for a composite convex minimization template with broad applications. Our approach combines smoothing and homotopy techniques under the CGM framework, and provably achieves the optimal O(1/k) convergence rate. We demonstrate that the same rate holds if the linear subproblems are solved approximately with additive or multiplicative error. In contrast with the relevant work, we are able to characterize the convergence when the non-smooth term is an indicator function. Specific applications of our framework include the non-smooth minimization, semidefinite programming, and minimization with linear inclusion constraints over a compact domain. Numerical evidence demonstrates the benefits of our framework</td>
</tr>
<tr id="bib_Yurtsever2018-HomotopyCGM" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Yurtsever2018-HomotopyCGM,
  author = {Yurtsever, A. and Fercoq, O. and Locatello, F. and Cevher, V.},
  title = {A Conditional Gradient Framework for Composite Convex Minimization with Applications to Semidefinite Programming},
  booktitle = {Proc. 35th Int. Conf. Machine Learning (ICML)},
  year = {2018},
  url = {http://proceedings.mlr.press/v80/yurtsever18a/yurtsever18a.pdf}
}
</pre></td>
</tr>
<tr id="Yurtsever2019-Cgal" class="entry">
	<td>Yurtsever, A., Fercoq, O. and Cevher, V. 
	(2019), 
	<i>"A Conditional Gradient-Based Augmented Lagrangian Framework"</i>
	
	, In Proc. 36th Int. Conf. Machine Learning (ICML).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Yurtsever2019-Cgal','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Yurtsever2019-Cgal','bibtex')">BibTeX</a>]
	
	 [<a href="http://proceedings.mlr.press/v97/yurtsever19a/yurtsever19a.pdf" target="_blank">PDF</a>]
	 [<a href="https://drive.google.com/file/d/1ySODPrL2bMesyUj3xW9oSj4_a1L0pwAG/view" target="_blank">Poster</a>]</p>
	</td>
</tr>
<tr id="abs_Yurtsever2019-Cgal" class="abstract noshow">
	<td><b>Abstract</b>: This paper considers a generic convex minimization template with affine constraints over a compact domain, which covers key semidefinite programming applications. The existing conditional gradient methods either do not apply to our template or are too slow in practice. To this end, we propose a new conditional gradient method, based on a unified treatment of smoothing and augmented Lagrangian frameworks. The proposed method maintains favorable properties of the classical conditional gradient method, such as cheap linear minimization oracle calls and sparse representation of the decision variable. We prove O(k^-1/2) convergence rate for our method in the objective residual and the feasibility gap. This rate is essentially the same as the state of the art CG-type methods for our problem template, but the proposed method is arguably superior in practice compared to existing methods in various applications.</td>
</tr>
<tr id="bib_Yurtsever2019-Cgal" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Yurtsever2019-Cgal,
  author = {Yurtsever, A. and Fercoq, O. and Cevher, V.},
  title = {A Conditional Gradient-Based Augmented Lagrangian Framework},
  booktitle = {Proc. 36th Int. Conf. Machine Learning (ICML)},
  year = {2019},
  url = {http://proceedings.mlr.press/v97/yurtsever19a/yurtsever19a.pdf}
}
</pre></td>
</tr>
<tr id="Yurtsever2021-ScalableSDP" class="entry">
	<td>Yurtsever, A., Tropp, J., Fercoq, O., Udell, M. and Cevher, V. 
	(2021), 
	<i>"Scalable Semidefinite Programming"</i>
	, SIAM Journal on Mathematics of Data Science.
	
	
	. arXiv:1912.02949.
	
	 
	 Vol. 3
	(1)
	, pp. 171-200.
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Yurtsever2021-ScalableSDP','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Yurtsever2021-ScalableSDP','bibtex')">BibTeX</a>]
	
	 [<a href="https://arxiv.org/pdf/1912.02949.pdf" target="_blank">PDF</a>]
	</p>
	</td>
</tr>
<tr id="abs_Yurtsever2021-ScalableSDP" class="abstract noshow">
	<td><b>Abstract</b>: Semidefinite programming (SDP) is a powerful framework from convex optimization that has striking potential for data science applications. This paper develops a provably correct algorithm for solving large SDP problems by economizing on both the storage and the arithmetic costs. Numerical evidence shows that the method is effective for a range of applications, including relaxations of MaxCut, abstract phase retrieval, and quadratic assignment. Running on a laptop, the algorithm can handle SDP instances where the matrix variable has over 10^13 entries.</td>
</tr>
<tr id="bib_Yurtsever2021-ScalableSDP" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@article{Yurtsever2021-ScalableSDP,
  author = {Yurtsever, A. and Tropp, J. and Fercoq, O. and Udell, M. and Cevher, V.},
  title = {Scalable Semidefinite Programming},
  journal = {SIAM Journal on Mathematics of Data Science},
  school = {arXiv:1912.02949},
  year = {2021},
  volume = {3},
  number = {1},
  pages = {171-200},
  url = {https://arxiv.org/pdf/1912.02949.pdf}
}
</pre></td>
</tr>
<tr id="Yurtsever2019-SpiderFW" class="entry">
	<td>Yurtsever, A., Suvrit, S. and Cevher, V. 
	(2019), 
	<i>"Conditional Gradient Method via Stochastic Path-Integrated Differential Estimators"</i>
	
	, In Proc. 36th Int. Conf. Machine Learning (ICML).
	
	
	
	 
	
	
	
	
	<p class="infolinks">[<a href="javascript:toggleInfo('Yurtsever2019-SpiderFW','abstract')">Abstract</a>]
	 
	[<a href="javascript:toggleInfo('Yurtsever2019-SpiderFW','bibtex')">BibTeX</a>]
	
	 [<a href="http://proceedings.mlr.press/v97/yurtsever19b/yurtsever19b.pdf" target="_blank">PDF</a>]
	 [<a href="https://drive.google.com/file/d/1jfBn8xNVFYHoQcSgq8ywVHdFgnAdZG2n/view" target="_blank">Poster</a>]</p>
	</td>
</tr>
<tr id="abs_Yurtsever2019-SpiderFW" class="abstract noshow">
	<td><b>Abstract</b>: We propose a class of variance-reduced stochastic conditional gradient methods. By adopting the recent stochastic path-integrated differential estimator technique (SPIDER) of Fang et. al. (2018) for the classical Frank-Wolfe (FW) method, we introduce SPIDER-FW for finite-sum minimization as well as the more general expectation minimization problems. SPIDER-FW enjoys superior complexity guarantees in the non-convex setting, while matching the best known FW variants in the convex case. We also extend our framework a la conditional gradient sliding (CGS) of Lan &amp; Zhou. (2016), and propose SPIDER-CGS.</td>
</tr>
<tr id="bib_Yurtsever2019-SpiderFW" class="bibtex noshow">
<td><b>BibTeX</b>:
<pre>
@conference{Yurtsever2019-SpiderFW,
  author = {Yurtsever, A. and Suvrit, S. and Cevher, V.},
  title = {Conditional Gradient Method via Stochastic Path-Integrated Differential Estimators},
  booktitle = {Proc. 36th Int. Conf. Machine Learning (ICML)},
  year = {2019},
  url = {http://proceedings.mlr.press/v97/yurtsever19b/yurtsever19b.pdf}
}
</pre></td>
</tr>
</tbody>
</table>
<footer>
<small>Please find the list ordered by citation count in my <a href="https://scholar.google.com/citations?user=wa_n-xYAAAAJ&hl=en" target="_blank">Google Scholar</a> page.</small>
<br>

<small>Created by <a href="http://jabref.sourceforge.net">JabRef</a> on 14/09/2025.</small>
</footer>
<!-- file generated by JabRef -->
</body>
