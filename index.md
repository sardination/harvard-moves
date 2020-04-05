## Harvard Moves!

Even while we're all off campus, it's important to keep moving! Join in to help your house beat the others in this friendly competition!

### Who will finish a year's worth of exercise first?

Keep track of how many minutes of exercise you do each day and report it [here](https://forms.gle/DM885kDhJ9gyZXvP9). The goal is to get your house to a *year's worth of exercise* - that's 525,600 minutes - first!

<svg></svg>

<script type="text/javascript" src="https://code.jquery.com/jquery-1.12.0.min.js"></script>
<script src="https://d3js.org/d3.v5.js"></script>

<script>

var spreadsheet_id = "1ioYTL8RvrMvwxpoKRv7smNDj6CjQbgJBiThVWuvB0KM",
    url = "https://spreadsheets.google.com/feeds/list/" +
          spreadsheet_id +
          "/default/public/values?alt=json";

var houses = [
    'Adams',
    'Cabot',
    'Currier',
    'Dudley',
    'Dunster',
    'Eliot',
    'Kirkland',
    'Leverett',
    'Lowell',
    'Mather',
    'Pfzorheimer',
    'Quincy',
    'Winthrop'
]

var colors = {
    'Adams': '#f03138',
    'Cabot': '#f0343b',
    'Currier': '#2d674c',
    'Dudley': '#58a6f0',
    'Dunster': '#ce0000',
    'Eliot': '#60b4f0',
    'Kirkland': '#f02825',
    'Leverett': '#338e1d',
    'Lowell': '#303af0',
    'Mather': '#f0563f',
    'Pfzorheimer': '#25eff0',
    'Quincy': '#862e22',
    'Winthrop': '#ff0000'
}

var house_info = {};
for (h_i = 0; h_i < houses.length; h_i++) {
    house_info[houses[h_i]] = {
        'total_count': 0,
        'records': [],
    }
}

// create d3 window
// create d3 chart of line progress for each house
var margin = {top: 20, right: 20, bottom: 30, left: 50},
    width = 960 - margin.left - margin.right,
    height = 500 - margin.top - margin.bottom;

// parse the date / time
var parseTime = d3.timeParse("%m/%d/%Y %H:%M:%S");

// set the ranges
var x = d3.scaleTime().range([0, width]);
var y = d3.scaleLinear().range([height, 0]);

// append the svg obgect to the body of the page
// appends a 'group' element to 'svg'
// moves the 'group' element to the top left margin
var svg = d3.select("svg")
    .attr("width", width + margin.left + margin.right)
    .attr("height", height + margin.top + margin.bottom)
    .append("g")
    .attr("transform",
          "translate(" + margin.left + "," + margin.top + ")");

// grab content from reporting spreadsheet and aggregate
$.get({
  url: url,
  success: function(response) {
    var data = response.feed.entry,
    len = data.length,
    i = 0,
    media_links = [];

    var current_day = null;
    var reported_emails = [];

    for (i = 0; i < len; i++) {
        timestamp_content = data[i].gsx$timestamp.$t.split(" ");
        day = timestamp_content[0];
        timestamp = data[i].gsx$timestamp.$t;
        email = data[i].gsx$emailaddress.$t;
        house = data[i].gsx$house.$t;
        minutes = parseInt(data[i].gsx$howmanyminutesdidyouexercisetoday.$t);

        if (day != current_day) {
            if (current_day == null) {
                for (h_i = 0; h_i < houses.length; h_i++) {
                    house_info[houses[h_i]]['records'].push({
                        timestamp: "4/5/2020 00:00:00",
                        email: "none@none.com",
                        house: house,
                        minutes: 0
                    })
                }
            }

            current_day = day;
            reported_emails = [];
        }

        if (!reported_emails.includes(email)) {
            house_info[house]['total_count'] += minutes;
            house_info[house]['records'].push({
                timestamp:timestamp,
                email: email,
                house: house,
                minutes: minutes
            })
        }

        media_link = data[i].gsx$addalinktoatwitterinstagrampostofyourselfifyouhaveone.$t;
        if (media_link != "") {
            media_links.push(media_link);
        }
    }


    // Get the data
    total_data = []
    for (h_i = 0; h_i < houses.length; h_i++) {
        house = houses[h_i];
        data = [];
        total_minutes = 0
        records = house_info[house]["records"];
        for (r_i = 0; r_i < records.length; r_i++) {
            total_minutes += records[r_i].minutes
            new_item = {
                timestamp: parseTime(records[r_i].timestamp),
                minutes: total_minutes
            }
            data.push(new_item);
            total_data.push(new_item);
        }
        var curr_date = new Date();
        var curr_timestamp = curr_date.getMonth() + "/" + curr_date.getDate() + "/" + curr_date.getYear() + " " + curr_date.getHours() + ":" + curr_date.getMinutes() + ":" + curr_date.getSeconds()
        final_item = {
            timestamp: parseTime(curr_timestamp),
            minutes: total_minutes
        }
        data.push(final_item);
        total_data.push(final_item);

        svg.append("path")
            .data([data])
            .attr("class", "line")
            .style("stroke", colors[house])
            .attr("d", d3.line()
                    .x(function(d) { return x(d.timestamp); })
                    .y(function(d) { return y(d.minutes); }));
    }

    // Scale the range of the data
    x.domain(d3.extent(total_data, function(d) { return d.timestamp; }));
    y.domain([0, d3.max(total_data, function(d) {
        return d.minutes; })]);

    // Add the X Axis
    svg.append("g")
      .attr("transform", "translate(0," + height + ")")
      .call(d3.axisBottom(x));

    // Add the Y Axis
    svg.append("g")
      .call(d3.axisLeft(y));
  }
});


</script>
