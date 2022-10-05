# Data-extraction-from-cppcl-for-Hong-Kong-property-trade-history

This is only for learning purpose, not for business

# -*- coding: utf-8 -*-
"""housing_price.ipynb

Automatically generated by Colaboratory.

Original file is located at
    https://colab.research.google.com/drive/1DYw9_Muzp-sfWjEey6dN2jKry23_iYpA

## Install important components like selenium and chrome driver
"""

!pip install selenium
!apt-get update # to update ubuntu to correctly run apt install
!apt install chromium-chromedriver
!cp /usr/lib/chromium-browser/chromedriver /usr/bin

"""# Import the installed library and attach chrome driver to access the website"""

import sys
sys.path.insert(0,'/usr/lib/chromium-browser/chromedriver')
from selenium import webdriver
from selenium.webdriver.common.by import By
chrome_options = webdriver.ChromeOptions()
chrome_options.add_argument('--headless')
chrome_options.add_argument('--no-sandbox')
chrome_options.add_argument('--disable-dev-shm-usage')
import csv

"""# Locate the important table by XPATH and loop the td and tr tag for data extraction"""

wd = webdriver.Chrome('chromedriver',options=chrome_options)

start_page = 1
end_page = 1063
folder_name = 2022
link = "http://cppcl.property.hk/tran_prop.php"
def generate_result(wd, page):
  wd.get(str(link))
  a = wd.execute_script("findForm_submit('page',"+str(page)+");")
  table = wd.find_element(By.XPATH, '//html/body/table/tbody/tr[2]/td/table/tbody/tr[3]/td/form/table[3]/tbody')
  rows = table.find_elements(By.CSS_SELECTOR, "tr") 

  with open(str(folder_name) + '.csv', 'a') as csv_file:
      writer = csv.writer(csv_file)
      idx = 0
      for row in rows:
          # except last row
          if(idx > 0 and idx < len(rows) - 3):
            print ("Row "+ str(idx) )   
            cols = row.find_elements(By.CSS_SELECTOR, "td") #note: index start from 0, 1 is col 2
            col_idx = 0
            arr = []
            for col in cols:
              arr.append(col.text)            
              # print ("Col "+ str(col_idx) )   
              # print (col.text) #prints text from the element
              col_idx = col_idx + 1
            print ("".join(arr))
            writer.writerow(arr)
          idx = idx + 1 

for i in range(start_page, end_page):
  print ("Page "+ str(i) ) 
  generate_result(wd, i)
# generate_result(wd, 1)

wd.close()
