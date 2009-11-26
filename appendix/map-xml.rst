.. _map-xml:

Appendice D: XML prodotto per una mappa con contenitori aperti
==============================================================

Di seguito un exempio di XML per una mappa con alcuni sottocontenitori aperti.
Per i singoli elementi sono stati lasciati solo gli attributi xml più significativi.

.. sourcecode:: xml

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE sanet_map [
		<!ELEMENT graph (subgraph, node, edge)>
		<!ELEMENT subgraph (subgraph, node, edge)>
		<!ELEMENT node EMPTY>
		<!ELEMENT edge EMPTY>
	]>
	<graph id="map_root" name="Sottocontenitori aperti" bb="">
		<subgraph id="container-2" name="Sedi e rack" icon="/img/container128x128.png" bg="">
			<subgraph id="container-3" name="01 container" icon="/img/container128x128.png" bg="">
				<node id="node-1" name="node-1" icon="/img/node128x128.png" y="55.0">
					<iface id="iface-4" href="/sanet/state/iface/4/"/>
					<iface id="iface-1" href="/sanet/state/iface/1/"/>
					<iface id="iface-2" href="/sanet/state/iface/2/"/>
					<iface id="iface-3" href="/sanet/state/iface/3/"/>
				</node>
				<node id="node-2" name="node-2" icon="/img/node128x128.png" y="53.0">
					<iface id="iface-6" href="/sanet/state/iface/6/"/>
					<iface id="iface-7" href="/sanet/state/iface/7/"/>
					<iface id="iface-5" href="/sanet/state/iface/5/"/>
					<iface id="iface-8" href="/sanet/state/iface/8/"/>
				</node>
				<node id="node-13" name="node-13" icon="/img/node128x128.png" y="176.0">
					<iface id="iface-9" href="/sanet/state/iface/9/"/>
					<iface id="iface-10" href="/sanet/state/iface/10/"/>
				</node>
			</subgraph>
			<subgraph id="container-4" name="02 container" icon="/img/container128x128.png" bg="bg/sky.jpg">
				<node id="node-39" name="node-39" icon="/img/node128x128.png" y="18.0">
					<iface id="iface-30" href="/sanet/state/iface/30/"/>
					<iface id="iface-28" href="/sanet/state/iface/28/"/>
					<iface id="iface-29" href="/sanet/state/iface/29/"/>
				</node>
				<node id="node-40" name="node-40" icon="/img/node128x128.png" y="18.0">
					<iface id="iface-33" href="/sanet/state/iface/33/"/>
					<iface id="iface-31" href="/sanet/state/iface/31/"/>
					<iface id="iface-32" href="/sanet/state/iface/32/"/>
				</node>
				<node id="node-41" name="node-41" icon="/img/node128x128.png" y="91.0">
					<iface id="iface-35" href="/sanet/state/iface/35/"/>
					<iface id="iface-38" href="/sanet/state/iface/38/"/>
					<iface id="iface-34" href="/sanet/state/iface/34/"/>
					<iface id="iface-40" href="/sanet/state/iface/40/"/>
					<iface id="iface-44" href="/sanet/state/iface/44/"/>
					<iface id="iface-41" href="/sanet/state/iface/41/"/>
					<iface id="iface-36" href="/sanet/state/iface/36/"/>
					<iface id="iface-37" href="/sanet/state/iface/37/"/>
					<iface id="iface-39" href="/sanet/state/iface/39/"/>
					<iface id="iface-42" href="/sanet/state/iface/42/"/>
					<iface id="iface-43" href="/sanet/state/iface/43/"/>
				</node>
				<node id="node-43" name="node-43" icon="/img/bigswitch128x128.png" y="194.0">
					<iface id="iface-87" href="/sanet/state/iface/87/"/>
					<iface id="iface-86" href="/sanet/state/iface/86/"/>
					<iface id="iface-84" href="/sanet/state/iface/84/"/>
					<iface id="iface-76" href="/sanet/state/iface/76/"/>
					<iface id="iface-275" href="/sanet/state/iface/275/"/>
					<iface id="iface-85" href="/sanet/state/iface/85/"/>
					<iface id="iface-78" href="/sanet/state/iface/78/"/>
					<iface id="iface-73" href="/sanet/state/iface/73/"/>
					<iface id="iface-74" href="/sanet/state/iface/74/"/>
					<iface id="iface-75" href="/sanet/state/iface/75/"/>
					<iface id="iface-68" href="/sanet/state/iface/68/"/>
					<iface id="iface-69" href="/sanet/state/iface/69/"/>
					<iface id="iface-276" href="/sanet/state/iface/276/"/>
					<iface id="iface-247" href="/sanet/state/iface/247/"/>
					<iface id="iface-72" href="/sanet/state/iface/72/"/>
					<iface id="iface-79" href="/sanet/state/iface/79/"/>
					<iface id="iface-80" href="/sanet/state/iface/80/"/>
					<iface id="iface-81" href="/sanet/state/iface/81/"/>
					<iface id="iface-82" href="/sanet/state/iface/82/"/>
					<iface id="iface-83" href="/sanet/state/iface/83/"/>
				</node>
			</subgraph>
			<subgraph id="container-6" name="04 container" icon="/img/container128x128.png" bg="bg/coverweb.jpg">
				<node id="node-68" name="node-68" icon="/img/internet128x128.png" y="0.0">
					<iface id="iface-251" href="/sanet/state/iface/251/"/>
					<iface id="iface-252" href="/sanet/state/iface/252/"/>
					<iface id="iface-253" href="/sanet/state/iface/253/"/>
					<iface id="iface-254" href="/sanet/state/iface/254/"/>
					<iface id="iface-255" href="/sanet/state/iface/255/"/>
					<iface id="iface-256" href="/sanet/state/iface/256/"/>
					<iface id="iface-257" href="/sanet/state/iface/257/"/>
					<iface id="iface-258" href="/sanet/state/iface/258/"/>
					<iface id="iface-268" href="/sanet/state/iface/268/"/>
				</node>
			</subgraph>
			<node id="container-5" name="container-5" icon="/img/container128x128.png" y="14.0">
				<iface id="iface-239" href="/sanet/state/iface/239/"/>
				<iface id="iface-241" href="/sanet/state/iface/241/"/>
				<iface id="iface-240" href="/sanet/state/iface/240/"/>
			</node>
			<node id="container-7" name="container-7" icon="/img/container128x128.png" y="63.0"/>
			<node id="container-8" name="container-8" icon="/img/container128x128.png" y="4.0"/>
			<node id="container-19" name="container-19" icon="/img/container128x128.png" y="0.0">
				<iface id="iface-270" href="/sanet/state/iface/270/"/>
				<iface id="iface-269" href="/sanet/state/iface/269/"/>
				<iface id="iface-267" href="/sanet/state/iface/267/"/>
				<iface id="iface-266" href="/sanet/state/iface/266/"/>
				<iface id="iface-265" href="/sanet/state/iface/265/"/>
				<iface id="iface-261" href="/sanet/state/iface/261/"/>
				<iface id="iface-263" href="/sanet/state/iface/263/"/>
				<iface id="iface-264" href="/sanet/state/iface/264/"/>
			</node>
		</subgraph>
		<edge fromID="node-1" toID="node-2" fromIfaceID="iface-2" toIfaceID="iface-5" pos=""/>
		<edge fromID="node-1" toID="node-2" fromIfaceID="iface-3" toIfaceID="iface-8" pos=""/>
		<edge fromID="node-2" toID="node-13" fromIfaceID="iface-6" toIfaceID="iface-9" pos=""/>
		<edge fromID="node-13" toID="node-41" fromIfaceID="iface-10" toIfaceID="iface-43" pos=""/>
		<edge fromID="node-39" toID="node-40" fromIfaceID="iface-30" toIfaceID="iface-33" pos=""/>
		<edge fromID="node-43" toID="container-5" fromIfaceID="iface-68" toIfaceID="iface-239" pos=""/>
		<edge fromID="node-43" toID="container-5" fromIfaceID="iface-69" toIfaceID="iface-240" pos=""/>
		<edge fromID="node-43" toID="container-5" fromIfaceID="iface-75" toIfaceID="iface-241" pos=""/>
		<edge fromID="container-19" toID="container-19" fromIfaceID="iface-263" toIfaceID="iface-270" pos=""/>
		<edge fromID="container-19" toID="container-19" fromIfaceID="iface-266" toIfaceID="iface-269" pos=""/>
		<edge fromID="container-19" toID="node-68" fromIfaceID="iface-267" toIfaceID="iface-268" pos=""/>
	</graph>
