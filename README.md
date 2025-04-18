# Calender

      let
         Source = "",
         StartDate = #date(2023,11,1),
         EndDate = #date(2024,2,29),
         Custom1 = List.Dates(StartDate,Number.From(EndDate-StartDate),#duration(1,0,0,0)),
         #"Converted to Table" = Table.FromList(Custom1, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
         #"Renamed Columns" = Table.RenameColumns(#"Converted to Table",{{"Column1", "Date"}}),
         #"Inserted Year" = Table.AddColumn(#"Renamed Columns", "Year", each Date.Year([Date]), Int64.Type),
         #"Inserted Month Name" = Table.AddColumn(#"Inserted Year", "Month Name", each Date.MonthName([Date]), type text),
          #"Inserted Week of Month" = Table.AddColumn(#"Inserted Month Name", "Week of Month", each Date.WeekOfMonth([Date]), Int64.Type)
      in
         #"Inserted Week of Month"
