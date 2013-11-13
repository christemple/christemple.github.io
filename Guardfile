guard 'sass', :input => '_assets/css'
guard 'coffeescript', :input => '_assets/javascripts'

guard :concat, type: "js", files: %w{html5shiv.min respond.min app}, input_dir: "_assets/javascripts", output: "js/app"
guard :concat, type: "css", files: %w{reset syntax app}, input_dir: "_assets/css", output: "css/app"