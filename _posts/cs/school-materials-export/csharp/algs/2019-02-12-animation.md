		<?php 
		$path = "../../../../src/material";
		require "../../../../src/web/header.php"; ?>

		<div style="position: relative;" id="animationZone">
			<div id="demo" style="position: relative;border:1px solid #d3d3d3; height:250px; width: 475px;">
				<canvas id="line" width="1" height="175" style="position: absolute;background-color:blue; left: 28px;bottom:0px;"></canvas>
			</div>
			<svg width="11" height="40" style="position: absolute;" id="arrow_moving">
				<defs>
					<marker id="arrow" markerWidth="13" markerHeight="13" refx="2" refy="5" orient="270">
						<path d="M10,5 L2,8 L5,5 L2,2" style="fill:red;" />
					</marker>
				</defs>

				<path d="M6,5 L6,10"
					  style="stroke:red; stroke-width: 1.25px; fill: none;
							 marker-end: url(#arrow);"
				/>
			</svg>
			<div id="legend" style="position: absolute; left: 490px;top:10px;border:1px solid #d3d3d3;width:120px; height:80px;">
				<canvas width="3" height="10" style="position: absolute;background-color:red;top:5px; left:5px;"></canvas>
				<span style="position: absolute;font-size:12px;left: 12px;top:3px;" id="legend-first">Větší</span>

				<canvas width="3" height="10" style="position: absolute;background-color:green;top:20px; left:5px;"></canvas>
				<span style="position: absolute;font-size:12px;left: 12px;top:17px;" id="legend-second">Menší</span>

				<canvas width="2" height="10" style="position: absolute;top:35px; left:5px;border:1px solid #d3d3d3;"></canvas>
				<span style="position: absolute;font-size:12px;left: 12px;top:33px;" id="legend-third">Neřazené</span>

				<svg width="11" height="40" style="position: absolute;top:50px; left:1px;" id="legend-arrow">

					<defs>
						<marker id="arrow" markerWidth="13" markerHeight="13" refx="2" refy="5" orient="270">
							<path d="M10,5 L2,8 L5,5 L2,2" style="fill:red;" />
						</marker>
					</defs>

					<path d="M6,5 L6,10"
						  style="stroke:red; stroke-width: 1.25px; fill: none;
								 marker-end: url(#arrow);"
					/>

				</svg>
				<span style="position: absolute;font-size:12px;left: 12px;top:48px;" id="legend-fourth">Aktuálně největší</span>
				<canvas id="legend-line" width="1" height="12" style="position: absolute;background-color:blue; left: 7px;top:65px;"></canvas>
				<span style="position: absolute;font-size:12px;left: 12px;top:63px;" id="legend-fifth">Limita cyklu</span>
			</div>
			<button class="mdl-button mdl-js-button mdl-button--fab mdl-button--colored"  style="position: absolute; top:10px; left:410px;" onClick="pause()">
				<i class="material-icons" id="status">play_arrow*
			</button>
		</div>