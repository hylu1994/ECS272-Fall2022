<template>
    <div id="bar"></div>
</template>

<script>
    import * as d3 from "d3";
import { object } from "vue-types";
    import testData from "../../assets/data/test.json"; /* Example of reading in data direct from file*/

    export default {
        name: 'BarChart',
        data() { // pass data to others
            return {
                prepared_data: undefined,
                colorScale: undefined
            }
        },
        props:{ // received data from others
            myBarchartData: Array,
            mySelection: Object,
            mySinger: String
        },
        watch: { 
            mySelection: function(newVal, oldVal) { // watch it
                this.prepareData(this.myBarchartData, this.mySelection);
                if (this.mySelection.id===1 || this.mySelection.id===2 || this.mySelection.id===3 || this.mySelection.id===4 || this.mySelection.id===5){
                    console.log(this.mySelection.id, "bar");
                    this.drawBarChart(this.prepared_data, "#bar", this.mySelection)
                }
                else{
                    console.log(this.mySelection.id, "stacked");
                    this.drawStackedBarChart(this.prepared_data, "#bar")
                }
            },
            mySinger: function(newVal, oldVal) { // watch it
                this.prepareData(this.myBarchartData, this.mySelection);
                if (this.mySelection.id===1 || this.mySelection.id===2 || this.mySelection.id===3 || this.mySelection.id===4 || this.mySelection.id===5){
                    console.log(this.mySelection.id, "bar");
                    this.drawBarChart(this.prepared_data, "#bar", this.mySelection)
                }
                else{
                    console.log(this.mySelection.id, "stacked");
                    this.drawStackedBarChart(this.prepared_data, "#bar")
                }
            }
        },
        mounted(){ // actually drawing
            this.prepareData(this.myBarchartData, this.mySelection);
            if (this.mySelection.id===1 || this.mySelection.id===2 || this.mySelection.id===3 || this.mySelection.id===4 || this.mySelection.id===5){
                console.log(this.mySelection.id, "bar");
                this.drawBarChart(this.prepared_data, "#bar", this.mySelection)
            }
            else{
                console.log(this.mySelection.id, "stacked");
                this.drawStackedBarChart(this.prepared_data, "#bar")
            }
        },
        methods: {
            prepareData(data, selection) {
                console.log("prepareData: ", selection.id);
                let year_min = 2025;
                let year_max = 1800;
                // console.log("singer type: ", typeof(this.mySinger));
                data.forEach(element => {
                    let artist = element.artists.split('\'');
                    let musician_log = [];
                    let flag = false;
                    let flag_collab = 0;
                    for (let i=0; i<artist.length; i++){
                        if (artist[i][0]!='[' && artist[i][0]!=']' && artist[i][0]!=','){
                            musician_log.push(artist[i]);
                        }
                        if (String(typeof(this.mySinger))==="string"){
                            if (artist[i]==this.mySinger)   flag = true;
                        }
                        else if (artist[i]==this.mySinger[0]) flag_collab += 1;
                        else if (artist[i]==this.mySinger[1]) flag_collab += 1;
                    }
                    if (flag == true){
                        if (element.year < year_min) year_min = element.year;
                        if (element.year > year_max) year_max = element.year;
                    }
                    else if (flag_collab == 2){
                        console.log(element.year);
                        if (element.year < year_min) year_min = element.year;
                        if (element.year > year_max) year_max = element.year;
                    }
                });

                let year_release = {};
                for (let i=year_min; i<=year_max; i++){
                    let tmp = String(i);
                    year_release[tmp] = 0;
                };
                let year_release_detail = {};
                for (let i=year_min; i<=year_max; i++){
                    let tmp = String(i);
                    year_release_detail[tmp] = [];
                };
                let year_release_total_index = {};
                for (let i=year_min; i<=year_max; i++){
                    let tmp = String(i);
                    year_release_total_index[tmp] = [];
                };
                let year_singer = {};
                for (let i=year_min; i<=year_max; i++){
                    let tmp = String(i);
                    const tmp_dict = {A:0, B:0, C:0, D:0, E:0};
                    year_singer[tmp] = tmp_dict;
                };
                let year_song = {};
                for (let i=year_min; i<=year_max; i++){
                    let tmp = String(i);
                    const tmp_dict = {A:[], B:[], C:[], D:[], E:[]};
                    year_song[tmp] = tmp_dict;
                };
                
                let total_index = 0;
                data.forEach(element => {
                    let artist = element.artists.split('\'');
                    let musician_log = [];
                    let flag = false;
                    let flag_collab = 0;
                    for (let i=0; i<artist.length; i++){
                        if (artist[i][0]!='[' && artist[i][0]!=']' && artist[i][0]!=','){
                            musician_log.push(artist[i]);
                        }
                        if (String(typeof(this.mySinger))==="string"){
                            if (artist[i]==this.mySinger)   flag = true;
                        }
                        else if (artist[i]==this.mySinger[0]) flag_collab += 1;
                        else if (artist[i]==this.mySinger[1]) flag_collab += 1;
                    }
                    if (flag == true){
                        year_release[String(element.year)] += 1;
                        year_release_detail[String(element.year)].push(String(element.name));
                        year_release_total_index[String(element.year)].push(total_index);
                        // year_singer[String(element.year)].songs[element.name] = element.popularity;
                        if (element.popularity < 20){
                            year_singer[String(element.year)]['A'] += parseInt(element.popularity);
                            year_song[String(element.year)]['A'].push(String(element.name) + ": " + String(element.popularity));
                        } 
                        else if (element.popularity < 40){
                            year_singer[String(element.year)]['B'] += parseInt(element.popularity);
                            year_song[String(element.year)]['B'].push(String(element.name) + ": " + String(element.popularity));
                        } 
                        else if (element.popularity < 60){
                            year_singer[String(element.year)]['C'] += parseInt(element.popularity);
                            year_song[String(element.year)]['C'].push(String(element.name) + ": " + String(element.popularity));
                        } 
                        else if (element.popularity < 80){
                            year_singer[String(element.year)]['D'] += parseInt(element.popularity);
                            year_song[String(element.year)]['D'].push(String(element.name) + ": " + String(element.popularity));
                        } 
                        else if (element.popularity < 101){
                            year_singer[String(element.year)]['E'] += parseInt(element.popularity);
                            year_song[String(element.year)]['E'].push(String(element.name) + ": " + String(element.popularity));
                        } 
                    }
                    else if (flag_collab == 2){
                        year_release[String(element.year)] += 1;
                        year_release_detail[String(element.year)].push(String(element.name));
                        year_release_total_index[String(element.year)].push(total_index);
                        if (element.popularity < 20){
                            year_singer[String(element.year)]['A'] += parseInt(element.popularity);
                            year_song[String(element.year)]['A'].push(String(element.name) + ": " + String(element.popularity));
                        } 
                        else if (element.popularity < 40){
                            year_singer[String(element.year)]['B'] += parseInt(element.popularity);
                            year_song[String(element.year)]['B'].push(String(element.name) + ": " + String(element.popularity));
                        } 
                        else if (element.popularity < 60){
                            year_singer[String(element.year)]['C'] += parseInt(element.popularity);
                            year_song[String(element.year)]['C'].push(String(element.name) + ": " + String(element.popularity));
                        } 
                        else if (element.popularity < 80){
                            year_singer[String(element.year)]['D'] += parseInt(element.popularity);
                            year_song[String(element.year)]['D'].push(String(element.name) + ": " + String(element.popularity));
                        } 
                        else if (element.popularity < 101){
                            year_singer[String(element.year)]['E'] += parseInt(element.popularity);
                            year_song[String(element.year)]['E'].push(String(element.name) + ": " + String(element.popularity));
                        } 
                    }
                    total_index += 1;
                });

                let year_release_prepared = [];
                let year_popularity = [];
                let year_popularity_A = [];
                let year_popularity_B = [];
                let year_popularity_C = [];
                let year_popularity_D = [];
                let year_popularity_E = [];
                Object.keys(year_release).forEach(key => {
                    const song_tmp = {
                        album: key, //year
                        songs: year_release[key], //song count
                        detail: year_release_detail[key], //songs detail
                        total_index: year_release_total_index[key]
                    };
                    year_release_prepared.push(song_tmp);
                });
                this.prepared_data = year_release_prepared;
                // Object.keys(year_singer).forEach(key => {
                //     Object.keys(year_singer[key]).forEach(k => {
                //         if (k=='A'){
                //             let pop_tmp = {
                //                 album: key,
                //                 popularity: "popularity 0-20",
                //                 songs: year_singer[key][k],
                //                 detail: year_song[key][k]
                //             };
                //             year_popularity.push(pop_tmp);
                //             year_popularity_A.push(pop_tmp);
                //         }
                //         else if (k=='B'){
                //             let pop_tmp = {
                //                 album: key,
                //                 popularity: "popularity 20-40",
                //                 songs: year_singer[key][k],
                //                 detail: year_song[key][k]
                //             };
                //             year_popularity.push(pop_tmp);
                //             year_popularity_B.push(pop_tmp);
                //         }
                //         else if (k=='C'){
                //             let pop_tmp = {
                //                 album: key,
                //                 popularity: "popularity 40-60",
                //                 songs: year_singer[key][k],
                //                 detail: year_song[key][k]
                //             };
                //             year_popularity.push(pop_tmp);
                //             year_popularity_C.push(pop_tmp);
                //         }
                //         else if (k=='D'){
                //             let pop_tmp = {
                //                 album: key,
                //                 popularity: "popularity 60-80",
                //                 songs: year_singer[key][k],
                //                 detail: year_song[key][k]
                //             };
                //             year_popularity.push(pop_tmp);
                //             year_popularity_D.push(pop_tmp);
                //         }
                //         else if (k=='E'){
                //             let pop_tmp = {
                //                 album: key,
                //                 popularity: "popularity 80-100",
                //                 songs: year_singer[key][k],
                //                 detail: year_song[key][k]
                //             };
                //             year_popularity.push(pop_tmp);
                //             year_popularity_E.push(pop_tmp);
                //         }
                //     });
                // });
                // if (selection.id === 1){
                //     this.prepared_data = year_popularity_A;
                // }
                // else if (selection.id === 2){
                //     this.prepared_data = year_popularity_B;
                // }
                // else if (selection.id === 3){
                //     this.prepared_data = year_popularity_C;
                // }
                // else if (selection.id === 4){
                //     this.prepared_data = year_popularity_D;
                // }
                // else if (selection.id === 5){
                //     this.prepared_data = year_popularity_E;
                // }
                // else{
                //     this.prepared_data = year_popularity;
                // }

                // let Z = d3.map(year_popularity, d => d.popularity);
                // let zDomain = Z;
                // zDomain = new d3.InternSet(zDomain);
                // let color = d3.scaleOrdinal(zDomain, d3.schemeSpectral[zDomain.size]);
                // let colors = [];
                // colors.push(color(0));
                // colors.push(color(1));
                // colors.push(color(2));
                // colors.push(color(3));
                // colors.push(color(4));
                // colors.push(color(5));
                // this.$emit('colorChange', colors);
            },

            drawBarChart(data, id, selection) {

                const forEmitThis = this;

                let flag_dbclick = false;
                let select_switch = 0;
                let select_item = undefined;

                const margin = { top: 10, right: 10, bottom: 40, left: 30 };
                // const height = 300;
                // const width = 500;

                let width  = d3.select(id).node().getBoundingClientRect().width;
                let height = d3.select(id).node().getBoundingClientRect().height;

                // Compute values.
                const X = d3.map(data, d => d.album);
                const Y = d3.map(data, d => d.songs);
                const D = d3.map(data, d => d.detail);
                const TI = d3.map(data, d => d.total_index);

                // Compute default domains, and unique the x-domain.
                let xDomain = X;
                let yDomain = [0, d3.max(Y)];
                xDomain = new d3.InternSet(xDomain);

                // Omit any data not present in the x-domain.
                const I = d3.range(X.length).filter(i => xDomain.has(X[i]));

                const xRange = [margin.left, width - margin.right];
                const yRange = [height - margin.bottom, margin.top];
                // Construct scales, axes, and formats.
                const xScale = d3.scaleBand(xDomain, xRange).padding(0.1);
                const yScale = d3.scaleLinear(yDomain, yRange);
                const xAxis = d3.axisBottom(xScale).tickSizeOuter(0);
                const yAxis = d3.axisLeft(yScale).ticks(height / 40, "");

                // // Compute titles.
                // if (title === undefined) {
                //     const formatValue = yScale.tickFormat(100, yFormat);
                //     title = i => `${X[i]}\n${formatValue(Y[i])}`;
                // } else {
                //     const O = d3.map(data, d => d);
                //     const T = title;
                //     title = i => T(O[i], i, data);
                // }

                d3.selectAll(".barchart").remove();
                let svg = d3.select(id).append("svg")
                    .attr("class", "barchart")
                    .attr("viewBox", [0, 0, width, height])
                    .attr("width", width + margin.left + margin.right)
                    .attr("height", height + margin.top + margin.bottom);

                // const svg = d3.create('svg')
                //     .attr('id', svgId)
                //     .attr('width', width)
                //     .attr('height', height)
                //     .attr('viewBox', [0, 0, width, height])
                //     .attr('style', 'max-width: 100%; height: auto; height: intrinsic;');

                const bar = svg.append('g')
                    .attr('fill', "#578")
                    .selectAll('rect')
                    .data(I)
                    .join('rect')
                    .attr('x', i => xScale(X[i]))
                    .attr('y', i => yScale(Y[i]))
                    .attr('height', i => yScale(0) - yScale(Y[i]))
                    .attr('width', xScale.bandwidth())
                    .attr('index', i => i)
                    .on("click", barclick)
                    .on("dblclick", bardbclick);
                
                function barclick(){
                    if (flag_dbclick==false && select_switch===2){
                        d3.select(this).transition()
                            .duration('50')
                            .attr('stroke', '#999')
                            .attr('stroke-width', 0);
                    }
                };

                function bardbclick(){
                    console.log(this.getAttribute('stroke-width'), select_switch);
                    if (flag_dbclick==false){
                        flag_dbclick = true;
                        // console.log(this.getAttribute('x1'), this.getAttribute('x2'), this.getAttribute('y1'), this.getAttribute('y2'));
                        select_item = D[this.getAttribute('index')];
                        let ti_tmp = TI[this.getAttribute('index')];
                        console.log(select_item);
                        let cx_tmp = this.getAttribute('x');
                        let cy_tmp = this.getAttribute('y');
                        d3.select(this).transition()
                            .duration('50')
                            .attr('stroke', '#0f0')
                            .attr('stroke-width', 2);
                        // for (let item_idx=0; item_idx<select_item.length; item_idx++){
                        //     if (item_idx>3) break;
                        //     svg.append("text")
                        //         .attr("x", cx_tmp)
                        //         .attr("y", cy_tmp-item_idx*10)
                        //         .attr("class", "click_select_bar")
                        //         .text(String(select_item[item_idx]) + ": " + ti_tmp[item_idx])
                        //         .style("font-size", "10px")
                        //         .style("opacity", 1);
                        // }
                        select_switch = 2;
                        
                        forEmitThis.$emit('selectSongsChange', ti_tmp);
                        forEmitThis.$emit('selectTextChange', select_item);
                    }
                    else if (parseInt(this.getAttribute('stroke-width'))===select_switch){
                        d3.select(this).transition()
                            .duration('50')
                            .attr('stroke-width', 0);
                        d3.selectAll(".click_select_bar").remove();
                        select_switch = 0;
                        flag_dbclick = false;
                        select_item = undefined;
                        let ti_tmp = [];
                        console.log(select_switch, select_item);
                        forEmitThis.$emit('selectSongsChange', ti_tmp);
                        forEmitThis.$emit('selectTextChange', []);
                    }
                };

                
                // add lengend for chosen colors.
                // svg.append("circle")
                //     .attr("cx", margin.left + 95)
                //     .attr("cy", height -15)
                //     .attr("r", 4)
                //     .style("fill", this.colorScale(0))
                // svg.append("circle")
                //     .attr("cx", margin.left + 145)
                //     .attr("cy", height -15)
                //     .attr("r", 4)
                //     .style("fill", this.colorScale(1))
                // svg.append("circle")
                //     .attr("cx", margin.left + 195)
                //     .attr("cy", height -15)
                //     .attr("r", 4)
                //     .style("fill", this.colorScale(2))
                // svg.append("circle")
                //     .attr("cx", margin.left + 245)
                //     .attr("cy", height -15)
                //     .attr("r", 4)
                //     .style("fill", this.colorScale(3))
                // svg.append("circle")
                //     .attr("cx", margin.left + 295)
                //     .attr("cy", height -15)
                //     .attr("r", 4)
                //     .style("fill", this.colorScale(4))
                
                // svg.append("text")
                //     .attr("x", margin.left)
                //     .attr("y", height -15)
                //     .text("Song Popularity:")
                //     .style("font-size", "9px")
                //     .attr("alignment-baseline","middle")
                // svg.append("text")
                //     .attr("x", margin.left + 100)
                //     .attr("y", height -15)
                //     .text("0-20")
                //     .style("font-size", "9px")
                //     .attr("alignment-baseline","middle")
                // svg.append("text")
                //     .attr("x", margin.left + 150)
                //     .attr("y", height -15)
                //     .text("20-40")
                //     .style("font-size", "9px")
                //     .attr("alignment-baseline","middle")
                // svg.append("text")
                //     .attr("x", margin.left + 200)
                //     .attr("y", height -15)
                //     .text("40-60")
                //     .style("font-size", "9px")
                //     .attr("alignment-baseline","middle")
                // svg.append("text")
                //     .attr("x", margin.left + 250)
                //     .attr("y", height -15)
                //     .text("60-80")
                //     .style("font-size", "9px")
                //     .attr("alignment-baseline","middle")
                // svg.append("text")
                //     .attr("x", margin.left + 300)
                //     .attr("y", height -15)
                //     .text("80-100")
                //     .style("font-size", "9px")
                //     .attr("alignment-baseline","middle")


                // add labels for axes.
                svg.append("g")
                    .attr('transform', `translate(0,${height - margin.bottom})`)
                    .call(xAxis)
                    .style("font-size", "8px")
                    .call(g =>
                        g .select(".tick:last-of-type text")
                        .clone()
                        .attr("text-anchor", "middle")
                        .attr("x", -(width - margin.left - margin.right) / 2)
                        .attr("y", margin.bottom - 10)
                        .attr("font-weight", "bold")
                        .text("Song Release Year")
                        );

                svg.append("g")
                    .attr('transform', `translate(${margin.left}, 0)`)
                    .call(yAxis)
                    .style("font-size", "8px")
                    .call(g =>
                        g
                        .select(".tick:last-of-type text")
                        .clone()
                        .attr("transform", `rotate(-90)`)
                        .attr("text-anchor", "middle")
                        .attr("y", -margin.left +7)
                        .attr("x", -((height - margin.bottom - margin.top)/2))
                        .attr("font-weight", "bold")
                        .text("Song Release Count")
                        );

                return svg.node();
            },
            drawStackedBarChart(data, id) {

                let flag_dbclick = false;
                let select_switch = 0;
                let select_item = undefined;

                const margin = { top: 20, right: 40, bottom: 60, left: 60 };
                // const height = 300;
                // const width = 500;

                let width  = d3.select(id).node().getBoundingClientRect().width;
                let height = d3.select(id).node().getBoundingClientRect().height;
                console.log(data);
                const chartData = data;
                const X = d3.map(chartData, d => d.album);
                const Y = d3.map(chartData, d => d.songs);
                const D = d3.map(chartData, d => d.detail);
                const Z = d3.map(chartData, d => d.popularity);

                let xDomain = X;
                let zDomain = Z;
                xDomain = new d3.InternSet(xDomain);
                zDomain = new d3.InternSet(zDomain);
                const xRange = [margin.left, width - margin.right];
                const yRange = [height - margin.bottom, margin.top];
                const I = d3.range(X.length);

                const series = d3.stack()
                    .keys(zDomain)
                    .value(([x, I], z) => Y[I.get(z)])
                    .order(d3.stackOrderNone)
                    .offset(d3.stackOffsetDiverging)
                    (d3.rollup(I, ([i]) => i, i => X[i], i => Z[i]))
                    .map(s => s.map(d => Object.assign(d, {i: d.data[1].get(s.key)})));

                // return series;
                const yDomain = d3.extent(series.flat(2));

                // Construct scales, axes, and formats.
                const xScale = d3.scaleBand(xDomain, xRange).paddingInner(0.1);
                const yScale = d3.scaleLinear(yDomain, yRange);
                // return zDomain
                // const colors = d3.schemeSpectral[zDomain.size];
                const color = d3.scaleOrdinal(zDomain, d3.schemeSpectral[zDomain.size]);
                this.colorScale = color;
                // return color(0);
                const xAxis = d3.axisBottom(xScale).tickSizeOuter(0);
                const yAxis = d3.axisLeft(yScale).ticks(height / 25, "");

                // const svg = d3.create('svg')
                //     .attr('id', "svg_1")
                //     .attr('viewBox', [0, 0, width, height])
                
                d3.selectAll(".barchart").remove();
                let svg = d3.select(id).append("svg")
                    .attr("class", "barchart")
                    .attr("viewBox", [0, 0, width, height])
                    .attr("width", width + margin.left + margin.right)
                    .attr("height", height + margin.top + margin.bottom);


                const bar = svg.append("g")
                    .selectAll("g")
                    .data(series)
                    .join("g")
                    .attr("fill", ([{i}]) => color(Z[i]))
                    .selectAll("rect")
                    .data(d => d)
                    .join("rect")
                    .attr("x", ({i}) => xScale(X[i]))
                    .attr("y", ([y1, y2]) => Math.min(yScale(y1), yScale(y2)))
                    .attr("height", ([y1, y2]) => Math.abs(yScale(y1) - yScale(y2)))
                    .attr("width", xScale.bandwidth())
                    .attr("index", ({i}) => i)
                    .on("click", stackbarclick)
                    .on("dblclick", stackbardbclick);
                
                function stackbarclick(){
                    console.log(this.getAttribute("index"));
                }
                
                function stackbarclick(){
                    if (flag_dbclick==false && select_switch===2){
                        d3.select(this).transition()
                            .duration('50')
                            .attr('stroke', '#999')
                            .attr('stroke-width', 0);
                    }
                };

                function stackbardbclick(){
                    console.log(this.getAttribute('stroke-width'), select_switch);
                    if (flag_dbclick==false){
                        flag_dbclick = true;
                        // console.log(this.getAttribute('x1'), this.getAttribute('x2'), this.getAttribute('y1'), this.getAttribute('y2'));
                        select_item = D[this.getAttribute('index')];
                        console.log(select_item);
                        let cx_tmp = this.getAttribute('x');
                        let cy_tmp = this.getAttribute('y');
                        d3.select(this).transition()
                            .duration('50')
                            .attr('stroke', '#0f0')
                            .attr('stroke-width', 2);
                        for (let item_idx=0; item_idx<select_item.length; item_idx++){
                            if (item_idx>3) break;
                            svg.append("text")
                                .attr("x", cx_tmp)
                                .attr("y", cy_tmp-item_idx*10)
                                .attr("class", "click_select_bar")
                                .text(String(select_item[item_idx]))
                                .style("font-size", "10px")
                                .style("opacity", 1);
                        }
                        select_switch = 2;
                        
                        // forEmitThis.$emit('selectSingerChange', select_item);
                    }
                    else if (parseInt(this.getAttribute('stroke-width'))===select_switch){
                        d3.select(this).transition()
                            .duration('50')
                            .attr('stroke-width', 0);
                        d3.selectAll(".click_select_bar").remove();
                        select_switch = 0;
                        flag_dbclick = false;
                        select_item = undefined;
                        console.log(select_switch, select_item);
                        // forEmitThis.$emit('selectSingerChange', select_item);
                    }
                };

                // add lengend for chosen colors.
                svg.append("circle")
                    .attr("cx", margin.left + 95)
                    .attr("cy", height -15)
                    .attr("r", 4)
                    .style("fill", color(0))
                svg.append("circle")
                    .attr("cx", margin.left + 145)
                    .attr("cy", height -15)
                    .attr("r", 4)
                    .style("fill", color(1))
                svg.append("circle")
                    .attr("cx", margin.left + 195)
                    .attr("cy", height -15)
                    .attr("r", 4)
                    .style("fill", color(2))
                svg.append("circle")
                    .attr("cx", margin.left + 245)
                    .attr("cy", height -15)
                    .attr("r", 4)
                    .style("fill", this.colorScale(3))
                svg.append("circle")
                    .attr("cx", margin.left + 295)
                    .attr("cy", height -15)
                    .attr("r", 4)
                    .style("fill", this.colorScale(4))
                
                svg.append("text")
                    .attr("x", margin.left)
                    .attr("y", height -15)
                    .text("Song Popularity:")
                    .style("font-size", "9px")
                    .attr("alignment-baseline","middle")
                svg.append("text")
                    .attr("x", margin.left + 100)
                    .attr("y", height -15)
                    .text("0-20")
                    .style("font-size", "9px")
                    .attr("alignment-baseline","middle")
                svg.append("text")
                    .attr("x", margin.left + 150)
                    .attr("y", height -15)
                    .text("20-40")
                    .style("font-size", "9px")
                    .attr("alignment-baseline","middle")
                svg.append("text")
                    .attr("x", margin.left + 200)
                    .attr("y", height -15)
                    .text("40-60")
                    .style("font-size", "9px")
                    .attr("alignment-baseline","middle")
                svg.append("text")
                    .attr("x", margin.left + 250)
                    .attr("y", height -15)
                    .text("60-80")
                    .style("font-size", "9px")
                    .attr("alignment-baseline","middle")
                svg.append("text")
                    .attr("x", margin.left + 300)
                    .attr("y", height -15)
                    .text("80-100")
                    .style("font-size", "9px")
                    .attr("alignment-baseline","middle")


                // add labels for axes.
                svg.append("g")
                    .attr('transform', `translate(0,${height - margin.bottom})`)
                    .call(xAxis)
                    .style("font-size", "8px")
                    .call(g =>
                        g .select(".tick:last-of-type text")
                        .clone()
                        .attr("text-anchor", "middle")
                        .attr("x", -(width - margin.left - margin.right) / 2)
                        .attr("y", margin.bottom - 35)
                        .attr("font-weight", "bold")
                        .text("Song Release Year")
                        );

                svg.append("g")
                    .attr('transform', `translate(${margin.left}, 0)`)
                    .call(yAxis)
                    .style("font-size", "8px")
                    .call(g =>
                        g
                        .select(".tick:last-of-type text")
                        .clone()
                        .attr("transform", `rotate(-90)`)
                        .attr("text-anchor", "middle")
                        .attr("y", -margin.left +10)
                        .attr("x", -((height - margin.bottom - margin.top)/2))
                        .attr("font-weight", "bold")
                        .text("Song Popularity Sum")
                        );

                return Object.assign(svg.node(), {scales: {color}});
            }
        }
    }

</script>


<style>
    #bar {
        width: 100%;
        height: 80%;
    }
</style>