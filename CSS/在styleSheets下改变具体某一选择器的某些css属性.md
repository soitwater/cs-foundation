function getstyle(sname) {
	for (let i = 0;i < document.styleSheets.length;i++) {
		let rules;
		if (document.styleSheets[i].cssRules) {
			rules = document.styleSheets[i].cssRules;
		} else {
			rules = document.styleSheets[i].rules;
		}
		for (let j = 0;j < rules.length;j++) {
			if (rules[j].selectorText === sname) {
				return rules[j].style;
			}
		}
	}
}

getstyle(".gridlayout").display = "inline";
