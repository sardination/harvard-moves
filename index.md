## Harvard Moves!

Even while we're all off campus, it's important to keep moving! Join in to help your house beat the others in this friendly competition!

[__REPORT YOUR EXERCISE HERE!__](https://docs.google.com/forms/d/e/1FAIpQLSf_WW86OAEi-CNlfTUqqjbQ8lSSZCu-tD7o0bJI6Kwesmz6LQ/viewform)

### #HarvardMoves

What have you been up to? Show off your workouts by posting to social media and using the hashtag __#HarvardMoves__! Tag your house accounts on your posts to see what everyone around campus has been doing.

<a href="https://twitter.com/intent/tweet?button_hashtag=HarvardMoves&ref_src=twsrc%5Etfw" class="twitter-hashtag-button" data-show-count="false">Tweet #HarvardMoves</a><script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script> or use __#HarvardMoves__ on Instagram

### Who will finish a year's worth of exercise first?

Keep track of how many minutes of exercise you do each day and report it [___here___](https://docs.google.com/forms/d/e/1FAIpQLSf_WW86OAEi-CNlfTUqqjbQ8lSSZCu-tD7o0bJI6Kwesmz6LQ/viewform). The goal is to get your house to a *year's worth of exercise* - that's 525,600 minutes - first! You can report up to 3 hours of exercise each day to help out your house.

<table><tr><th>House</th><th>Minutes Exercised</th><th>Time of the Year Reached</th><th>Finished a Year?</th></tr></table>


<script type="text/javascript" src="https://code.jquery.com/jquery-1.12.0.min.js"></script>
<script>

var spreadsheet_id = "1kLioSypBWlQJtU_Z4LP8-7J7da6ElbB4dWGgT9EFU64",
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
    'Pforzheimer',
    'Quincy',
    'Winthrop'
]

var house_info = {};
for (h_i = 0; h_i < houses.length; h_i++) {
    house_info[houses[h_i]] = {
        'total_count': 0,
        'time_reached': null,
        'records': [],
    }
}

var min_in_year = 525600;

// grab content from reporting spreadsheet and aggregate
$.get({
  url: url,
  success: function(response) {
    var data = response.feed.entry;
    var len = 0;
    if (data) {
        len = data.length
    }
    var i = 0;
    var media_links = [];

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
            if (house_info[house]['total_count'] >= min_in_year) {
                house_info[house]['time_reached'] = timestamp;
            }
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

    var sorted_houses = [];
    for (var house in house_info) {
        sorted_houses.push([house, house_info[house]['time_reached'], house_info[house]['total_count']]);
    }

    sorted_houses.sort(function(a, b) {
        if (a[1] != null && b[1] != null) {
            return a[1] - b[1]; // first date finished
        } else if (a[1] != null) {
            return -1; // a finished, b not
        } else if (b[1] != null) {
            return 1; // b finished, a not
        } else {
            return b[2] - a[2]; // neither finished - which has more minutes?
        }
    })

    var table_obj = $('table');
    const monthNames = ["January", "February", "March", "April", "May", "June",
      "July", "August", "September", "October", "November", "December"
    ];

    for (h_i = 0; h_i < houses.length; h_i++) {
        new_row = "<tr><td>" + sorted_houses[h_i][0] + "</td><td>" + sorted_houses[h_i][2] + "</td>"
        var reached_day = new Date(2020, 0, 1);
        reached_day.setMinutes(house_info[sorted_houses[h_i][0]]["total_count"]);
        new_row += "<td>" + monthNames[reached_day.getMonth()] + " " + reached_day.getDate() + " " + ('0' + reached_day.getHours()).slice(-2) + ":" + ('0' + reached_day.getMinutes()).slice(-2) + "</td>"
        time_reached = house_info[sorted_houses[h_i][0]]["time_reached"]
        if (time_reached != null) {
            new_row += "<td>Finished on " + monthNames[time_reached.getMonth()] + " " + time_reached.getDate() + ", " + time_reached.getYear() + "</td></tr>";
        } else {
            new_row += "<td>Keep going!</td></tr>"
        }
        table_obj.append(new_row)
    }
  }
});


</script>
