# Expired-Azure-AD-service-Principal-along-with-owner-details
Expired Azure AD service Principal along with owner details
 
# This code with export the list of Azure AD ServicePrincipal expired key and password credentials. This code will compare Password credentials and Key Credentials with today's date.
 
 
Connect-AzureAD
$results = @()
Get-AzureADServicePrincipal -All $true | %{  
                             $app = $_
                             $owner = Get-AzureADServicePrincipalOwner -ObjectId $_.ObjectID

                             $app.PasswordCredentials | where {$_.enddate -notlike “” -and $_.enddate -LE ((get-date).AddDays(0))} |
                                %{ 
                                    $results += [PSCustomObject] @{
                                            CredentialType = "PasswordCredentials"
                                            DisplayName = $app.DisplayName; 
                                            EndDate = $_.enddate;
                                            ObjectId = $app.ObjectId
                                            Owners = (@($owner.UserPrincipalName) -join ',');
                                            OwnerName = (@($owner.DisplayName) -join ',');
                                            
                                            }
                                 } 
                                  
                             $app.KeyCredentials | where {$_.enddate -notlike “” -and $_.enddate -LE ((get-date).AddDays(0))} |
                                %{ 
                                    $results += [PSCustomObject] @{
                                            CredentialType = "KeyCredentials"                                        
                                            DisplayName = $app.DisplayName; 
                                            EndDate = $_.enddate;
                                            ObjectID = $app.ObjectId
                                            Owners = (@($owner.UserPrincipalName) -join ',');
                                            OwnerName = (@($owner.DisplayName) -join ',');

                                        }
                                 }  
}
                                                    
                          
$results | FT -AutoSize 
# export to a CSV file
$results | Export-Csv -Path "AzureADExpiredSP.csv" -NoTypeInformation


Get-AzureADServicePrincipal -ObjectId "febe99a8-6fdf-4d29-a82d-8acf99ee25fb” 

