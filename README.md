# Calendar Table

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

# Complete Calendar Table 


         

# ActiveDirector

            let
                Source = ActiveDirectory.Domains("corp.xxx.com"),
                corp.xxx.com = Source{[Domain="corp.xxx.com"]}[#"Object Categories"],
                user1 = corp.xxx.com{[Category="user"]}[Objects],
                #"Removed Other Columns" = Table.SelectColumns(user1,{"displayName", "organizationalPerson"}),
                #"Expanded organizationalPerson" = Table.ExpandRecordColumn(#"Removed Other Columns", "organizationalPerson", {"assistant", "businessRoles", "c", "co", "comment", "company", "countryCode", "department", "destinationIndicator", "division", "employeeID", "employeeNumber", "employeeType", "facsimileTelephoneNumber", "generationQualifier", "givenName", "homePhone", "homePostalAddress", "houseIdentifier", "initials", "internationalISDNNumber", "ipPhone", "l", "mail", "manager", "mhsORAddress", "middleName", "mobile", "msDS-AllowedToActOnBehalfOfOtherIdentity", "msDS-AllowedToDelegateTo", "msDS-HABSeniorityIndex", "msDS-PhoneticCompanyName", "msDS-PhoneticDepartment", "msDS-PhoneticDisplayName", "msDS-PhoneticFirstName", "msDS-PhoneticLastName", "msExchHouseIdentifier", "msExchUserCulture", "o", "otherFacsimileTelephoneNumber", "otherHomePhone", "otherIpPhone", "otherMailbox", "otherMobile", "otherPager", "otherTelephone", "ou", "pager", "personalPager", "personalTitle", "physicalDeliveryOfficeName", "postalAddress", "postalCode", "postOfficeBox", "preferredDeliveryMethod", "primaryInternationalISDNNumber", "primaryTelexNumber", "registeredAddress", "st", "street", "streetAddress", "telephoneAssistant", "teletexTerminalIdentifier", "telexNumber", "thumbnailLogo", "thumbnailPhoto", "title", "x121Address"}, {"assistant", "businessRoles", "c", "co", "comment", "company", "countryCode", "department", "destinationIndicator", "division", "employeeID", "employeeNumber", "employeeType", "facsimileTelephoneNumber", "generationQualifier", "givenName", "homePhone", "homePostalAddress", "houseIdentifier", "initials", "internationalISDNNumber", "ipPhone", "l", "mail", "manager", "mhsORAddress", "middleName", "mobile", "msDS-AllowedToActOnBehalfOfOtherIdentity", "msDS-AllowedToDelegateTo", "msDS-HABSeniorityIndex", "msDS-PhoneticCompanyName", "msDS-PhoneticDepartment", "msDS-PhoneticDisplayName", "msDS-PhoneticFirstName", "msDS-PhoneticLastName", "msExchHouseIdentifier", "msExchUserCulture", "o", "otherFacsimileTelephoneNumber", "otherHomePhone", "otherIpPhone", "otherMailbox", "otherMobile", "otherPager", "otherTelephone", "ou", "pager", "personalPager", "personalTitle", "physicalDeliveryOfficeName", "postalAddress", "postalCode", "postOfficeBox", "preferredDeliveryMethod", "primaryInternationalISDNNumber", "primaryTelexNumber", "registeredAddress", "st", "street", "streetAddress", "telephoneAssistant", "teletexTerminalIdentifier", "telexNumber", "thumbnailLogo", "thumbnailPhoto", "title", "x121Address"}),
                #"Filtered Rows" = Table.SelectRows(#"Expanded organizationalPerson", each [displayName] <> null and [displayName] <> ""),
                #"Removed Other Columns1" = Table.SelectColumns(#"Filtered Rows",{"displayName", "c", "l", "mobile", "postalCode", "title"}),
                #"Filtered Rows1" = Table.SelectRows(#"Removed Other Columns1", each ([c] = "IN")),
                #"Filtered Rows2" = Table.SelectRows(#"Filtered Rows1", each [mobile] <> null and [mobile] <> ""),
                #"Added Custom" = Table.AddColumn(#"Filtered Rows2", "IsValidContact", each let
                    phone = Text.Trim([mobile]),
                    digitsOnly = Text.Select(phone, {"0".."9"}),
                    startsWith91 = Text.StartsWith(phone, "+91"),
                    isValidLength = Text.Length(digitsOnly) = 12,
                    isNotAllZeros = digitsOnly <> "910000000000",
                    hasValidBody = Text.Middle(digitsOnly, 2, 10) <> Text.Repeat("0", 10),
                    isNumeric = Value.Is(Number.From(digitsOnly), type number)
                in
                    if startsWith91 and isValidLength and isNotAllZeros and hasValidBody and isNumeric then "Valid" else "Invalid"),
                #"Filtered Rows3" = Table.SelectRows(#"Added Custom", each ([IsValidContact] = "Valid"))
            in
                #"Filtered Rows3"
