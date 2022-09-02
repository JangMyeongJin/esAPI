<template>
  <div class="header">
    <div class="gnb-wrap">
		<div class="header-row"></div>
	</div>
    <div id="lnbWrapper" class="lnb-wrap">
		<div class="header-row">
			<div class="header-logo">
				<h1><a href="http://localhost:8080/" title="E-GEN"><img src="../../images/Pro-Search.png" alt="Pro10" width="180" height="60"></a></h1>
			</div>
            <h2 class="screen-out">메뉴 영역</h2>
			<div class="lnb type-egen">
                <ul id="menuList">
                    <li><a href="#" onClick="" class="lnb-depth1" title="MENU1">메뉴1</a></li>
                    <li><a href="#" onClick="" class="lnb-depth1" title="MENU2">메뉴2</a></li>
                    <li><a href="#" onClick="" class="lnb-depth1" title="MENU3">메뉴3</a></li>
                    <li>
                        <a href="#" onClick="" class="lnb-depth1" title="MENU4">메뉴4</a>
                        <div class="lnb-depth2-group" style="display:none">
							<ul>
								<li><a href="#" class="lnb-depth2" onClick="">menu1</a></li>
                                <li><a href="#" class="lnb-depth2" onClick="">menu2</a></li>
                                <li><a href="#" class="lnb-depth2" onClick="">menu3</a></li>
							</ul>
						</div>
                    </li>
                </ul>
			</div>
		</div>
	</div>
    <div class="lnb-depth2-bg" style="display:none">
        <div class="header-row"></div>
	</div>
  </div>
</template>

<script>
export default {

}
</script>

<style>
@import '../../css/common.css';

</style>