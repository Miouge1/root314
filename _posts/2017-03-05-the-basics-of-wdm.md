---
layout:     post
title:      The basics of Wavelength Division Multiplexing
date:       2017-03-05 12:00:00
author:     Maxime
header-img: "img/network2-bg.jpg"
image: "img/network2-sq.jpg"
tags: [network, wdm]
comments: true
---
Wavelength Division Multiplexing (WDM) is a multiplexing method which uses different colors (or wavelengths) of light. Where traditional optics allow a single channel of communication on a fiber pair, WDM deployments allow to carry up to 96 channels on a single fiber pair. Channels can support different technologies and different speeds, for example you can mix 1G Ethernet, 10G Ethernet and 4G Fiber Channel on the same fiber run.

### Usage

Since it is a multiplexing technology, we combine (mux) the transmission and we split (demux) the reception. With a typical fiber pairs, this is done by a mux/demux passive box as illustrated below.

![wdm usage]({{site.url}}/img/posts/mux-demux.svg)

The optics used in a WDM setup are called colored optics (tuned to a specific wavelength) therefore the color on each end of the fiber must match.

### CWDM vs DWDM
WDM exists in two variety: Coarse (CWDM) and Dense (DWDM), each using its own range of wavelengths.  Since their wavelength range overlaps, they can co-exist on the same link to some extent.

| Name | Channels| Wavelength | Cost | Use case |
|------|---------|------------|------|----------|
| CWDM | 18      | 1270-1610nm| Low  | Short distance|
| DWDM | 96      | 1520-1577nm| High | Medium/long distance|

### Reducing interconnect costs
WDM reduces the interconnection costs by using the same fiber pair for many links.
Let's say we want 60Gbps bandwidth (6x10 Gbps) between two point of presence (POP A and B) with dark fiber between them.

To make this a fair example I will assume the following prices:
* Dark fiber: 100 &euro;/mo/fiber pair
* Traditional LR optic 10Gbps SFP+: 30 &euro;
* CWDM optic 10Gbps SFP+: 100 &euro;/unit
* Mux-Demux: 700 &euro;/unit

#### Traditional approach
We need 6 dark fiber pairs (600 &euro;/mo) and 12 optics (360 &euro;). Costing a total of 21 960 &euro; over 3 years.

#### CWDM
Since we need 6 channels CWDM (up to 16 channels) over a few hundred meters (different rooms) will work just fine.
Of course we will need only 1 fiber pair (100 &euro;/mo), and 12 CWDM optics (1200 &euro;).
We will also use 1 Mux-Demux at each location (1400 &euro;).

Yielding a total cost of 6 200 &euro;, 70% less than the traditional setup.

### Total Cost of Ownership calculator

You can use the following calculator to estimate the TCO of a WDM deployment based on the number of channels.
The example costs are based on online prices as of March 2017.

<form ng-controller="CalculatorController" class="well">
<div class="row">
  <div class="btn-group">
    <button type="button" class="btn btn-default dropdown-toggle" data-toggle="dropdown" aria-haspopup="true"   aria-expanded="false">
    Examples <span class="caret"></span>
    </button>
    <ul class="dropdown-menu">
      <li><a ng-click="channels = 4; price_wdm = 100; price_mux = 220;">4 channels CWDM</a></li>
      <li><a ng-click="channels = 4; price_wdm = 330; price_mux = 310;">4 channels DWDM</a></li>
      <li><a ng-click="channels = 8; price_wdm = 100; price_mux = 390;">8 channels CWDM</a></li>
      <li><a ng-click="channels = 8; price_wdm = 330; price_mux = 580;">8 channels DWDM</a></li>
      <li><a ng-click="channels = 16; price_wdm = 165; price_mux = 740;">16 channels CWDM</a></li>
      <li><a ng-click="channels = 16; price_wdm = 330; price_mux = 1000;">16 channels DWDM</a></li>
      <li><a ng-click="channels = 40; price_wdm = 330; price_mux = 1600;">40 channels DWDM</a></li>
      <li><a ng-click="channels = 80; price_wdm = 440; price_mux = 8500;">80 channels DWDM</a></li>
      <li><a ng-click="channels = 96; price_wdm = 440; price_mux = 7900;">96 channels DWDM</a></li>
    </ul>
  </div>
</div>


<div class="row">
    <div class="col-md-6">
    <h4>Setup</h4>
    {% include input.html label="Channels" name="channels" type="number" %}
    {% include input.html label="TCO (in months)" name="period" type="number" %}
    </div>
    <div class="col-md-6">
    <h4>Prices</h4>
      {% include input.html label="Dark fiber (in &euro;/mo/fiber pair)" name="price_fiber" type="number" %}
      {% include input.html label="Standard optic (in &euro;/unit)" name="price_optic" type="number" %}
      {% include input.html label="WDM optic (in &euro;/unit)" name="price_wdm" type="number" %}
      {% include input.html label="Mux-Demux (in &euro;/unit)" name="price_mux" type="number" %}
      </div>

</div>
<h3>Results</h3>
<ul>
  <li>TCO without WDM: <span ng-bind="tco_trad() | number:0"></span> &euro;</li>
  <li>TCO with WDM: <span ng-bind="tco_wdm() | number:0"></span> &euro;</li>
</ul>
</form>

<!-- AngularJS -->
<script src="//ajax.googleapis.com/ajax/libs/angularjs/1.5.6/angular.min.js"></script>
<script>
angular.module('Root314', [])
  .controller('CalculatorController', ['$scope', function($scope) {

    $scope.period = 36;
    $scope.channels = 6;

    $scope.price_fiber = 100;
    $scope.price_optic = 30;
    $scope.price_wdm = 100;
    $scope.price_mux = 700;

    $scope.set_example = function(id) {
      $scope.channels = $scope.examples[id].channels;
      $scope.price_wdm = $scope.examples[id].price_wdm;
      $scope.price_mux = $scope.examples[id].price_mux;
    };
    $scope.tco_trad = function() {
      return $scope.channels*($scope.price_fiber*$scope.period + 2*$scope.price_optic);
    };
    $scope.tco_wdm = function() {
      return $scope.price_fiber*$scope.period + 2*($scope.channels*$scope.price_wdm + $scope.price_mux);
    };
  }]);
</script>

### Caveats

Using WDM requires additional documentation to keep track of which wavelengths are used by which links. Be mindful of the physical layer and do not put redundant links on the same fiber pair.

### Conclusion

WDM is a great tool to deliver high speed (up to 960 Gbps) on a single fiber pair and to keep costs down.
