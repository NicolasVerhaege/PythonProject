#PythonProject
   "cell_type": "code",
   "execution_count": 58,
   "id": "9860fee0-de38-4cd2-936b-7479745b0f4e",
   "metadata": {},
   "outputs": [],
   "source": [
    "import yfinance as yf\n",
    "import pandas as pd\n",
    "import requests\n",
    "from bs4 import BeautifulSoup\n",
    "import plotly.graph_objects as go\n",
    "from plotly.subplots import make_subplots"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "c2d6a13c-efae-4024-85e5-4edda1895c92",
   "metadata": {},
   "source": [
    "## Define Graphing Function\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "4c0e1b5f-6137-4d71-b130-5aabd2cfb7f5",
   "metadata": {},
   "source": [
    "In this section, we define the function `make_graph`. You don't have to know how the function works, you should only care about the inputs. It takes a dataframe with stock data (dataframe must contain Date and Close columns), a dataframe with revenue data (dataframe must contain Date and Revenue columns), and the name of the stock.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "0dcaaa5e-e5ed-4337-beb4-134b5da043d6",
   "metadata": {},
   "outputs": [],
   "source": [
    "def make_graph(stock_data, revenue_data, stock):\n",
    "    fig = make_subplots(rows=2, cols=1, shared_xaxes=True, subplot_titles=(\"Historical Share Price\", \"Historical Revenue\"), vertical_spacing = .3)\n",
    "    stock_data_specific = stock_data[stock_data.Date <= '2021--06-14']\n",
    "    revenue_data_specific = revenue_data[revenue_data.Date <= '2021-04-30']\n",
    "    fig.add_trace(go.Scatter(x=pd.to_datetime(stock_data_specific.Date, infer_datetime_format=True), y=stock_data_specific.Close.astype(\"float\"), name=\"Share Price\"), row=1, col=1)\n",
    "    fig.add_trace(go.Scatter(x=pd.to_datetime(revenue_data_specific.Date, infer_datetime_format=True), y=revenue_data_specific.Revenue.astype(\"float\"), name=\"Revenue\"), row=2, col=1)\n",
    "    fig.update_xaxes(title_text=\"Date\", row=1, col=1)\n",
    "    fig.update_xaxes(title_text=\"Date\", row=2, col=1)\n",
    "    fig.update_yaxes(title_text=\"Price ($US)\", row=1, col=1)\n",
    "    fig.update_yaxes(title_text=\"Revenue ($US Millions)\", row=2, col=1)\n",
    "    fig.update_layout(showlegend=False,\n",
    "    height=900,\n",
    "    title=stock,\n",
    "    xaxis_rangeslider_visible=True)\n",
    "    fig.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "fbaa3d01-1420-4087-9d79-3a56522c1610",
   "metadata": {},
   "source": [
    "## Question 1: Use yfinance to Extract Stock Data\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "d574fd10-1aab-4e46-a924-53d028709d24",
   "metadata": {},
   "source": [
    "Using the `Ticker` function enter the ticker symbol of the stock we want to extract data on to create a ticker object. The stock is Tesla and its ticker symbol is `TSLA`.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 10,
   "id": "408e0d71-3e76-4731-8cb0-60e7efd7c9ae",
   "metadata": {},
   "outputs": [],
   "source": [
    "Tesla = yf.Ticker(\"TSLA\")"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "13c78686-b22b-490c-acac-b66d49024e46",
   "metadata": {},
   "source": [
    "Using the ticker object and the function `history` extract stock information and save it in a dataframe named `tesla_data`. Set the `period` parameter to `max` so we get information for the maximum amount of time.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 12,
   "id": "7f9763e4-9122-41ec-9443-a53343c59b60",
   "metadata": {},
   "outputs": [],
   "source": [
    "Tesla_data = Tesla.history(period='max')"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "15032e00-90d7-455e-adcd-a1eeec54d6e0",
   "metadata": {},
   "source": [
    "**Reset the index** using the `reset_index(inplace=True)` function on the tesla_data DataFrame and display the first five rows of the `tesla_data` dataframe using the `head` function. Take a screenshot of the results and code from the beginning of Question 1 to the results below.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "id": "0123c47b-8378-4c7f-99be-80e571416ebd",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Date</th>\n",
       "      <th>Open</th>\n",
       "      <th>High</th>\n",
       "      <th>Low</th>\n",
       "      <th>Close</th>\n",
       "      <th>Volume</th>\n",
       "      <th>Dividends</th>\n",
       "      <th>Stock Splits</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>2010-06-29</td>\n",
       "      <td>1.266667</td>\n",
       "      <td>1.666667</td>\n",
       "      <td>1.169333</td>\n",
       "      <td>1.592667</td>\n",
       "      <td>281494500</td>\n",
       "      <td>0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2010-06-30</td>\n",
       "      <td>1.719333</td>\n",
       "      <td>2.028000</td>\n",
       "      <td>1.553333</td>\n",
       "      <td>1.588667</td>\n",
       "      <td>257806500</td>\n",
       "      <td>0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>2010-07-01</td>\n",
       "      <td>1.666667</td>\n",
       "      <td>1.728000</td>\n",
       "      <td>1.351333</td>\n",
       "      <td>1.464000</td>\n",
       "      <td>123282000</td>\n",
       "      <td>0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>2010-07-02</td>\n",
       "      <td>1.533333</td>\n",
       "      <td>1.540000</td>\n",
       "      <td>1.247333</td>\n",
       "      <td>1.280000</td>\n",
       "      <td>77097000</td>\n",
       "      <td>0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>2010-07-06</td>\n",
       "      <td>1.333333</td>\n",
       "      <td>1.333333</td>\n",
       "      <td>1.055333</td>\n",
       "      <td>1.074000</td>\n",
       "      <td>103003500</td>\n",
       "      <td>0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "        Date      Open      High       Low     Close     Volume  Dividends  \\\n",
       "0 2010-06-29  1.266667  1.666667  1.169333  1.592667  281494500          0   \n",
       "1 2010-06-30  1.719333  2.028000  1.553333  1.588667  257806500          0   \n",
       "2 2010-07-01  1.666667  1.728000  1.351333  1.464000  123282000          0   \n",
       "3 2010-07-02  1.533333  1.540000  1.247333  1.280000   77097000          0   \n",
       "4 2010-07-06  1.333333  1.333333  1.055333  1.074000  103003500          0   \n",
       "\n",
       "   Stock Splits  \n",
       "0           0.0  \n",
       "1           0.0  \n",
       "2           0.0  \n",
       "3           0.0  \n",
       "4           0.0  "
      ]
     },
     "execution_count": 13,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "Tesla_data.reset_index(inplace=True)\n",
    "Tesla_data.head()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "b94b5dab-a6f1-40eb-b836-0062988464db",
   "metadata": {},
   "source": [
    "## Question 2: Use Webscraping to Extract Tesla Revenue Data\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "10c4befd-c47e-4592-a72e-7c0b5c678068",
   "metadata": {},
   "source": [
    "Use the `requests` library to download the webpage https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm Save the text of the response as a variable named `html_data`.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 21,
   "id": "f65ee884-d9a1-424a-bcb2-bab7e3fbc53f",
   "metadata": {},
   "outputs": [],
   "source": [
    "url = \"https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm\"\n",
    "html_data = (requests.get(url)).text\n",
    "import requests"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "e08fb22f-128a-4f99-8344-dd18d6279ab6",
   "metadata": {},
   "source": [
    "Parse the html data using `beautiful_soup`.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 23,
   "id": "208b38b4-260c-4098-9b1e-9e88baed91c7",
   "metadata": {},
   "outputs": [],
   "source": [
    "from bs4 import BeautifulSoup\n",
    "soup = BeautifulSoup(html_data, 'html.parser')"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "2beb668c-ce60-4121-96fc-0aa47465f941",
   "metadata": {},
   "source": [
    "Using `BeautifulSoup` or the `read_html` function extract the table with `Tesla Quarterly Revenue` and store it into a dataframe named `tesla_revenue`. The dataframe should have columns `Date` and `Revenue`.\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "e6d73b96-e834-49d0-a325-4341ab772ad8",
   "metadata": {},
   "source": [
    "<details><summary>Click here if you need help locating the table</summary>\n",
    "\n",
    "```\n",
    "    \n",
    "Below is the code to isolate the table, you will now need to loop through the rows and columns like in the previous lab\n",
    "    \n",
    "soup.find_all(\"tbody\")[1]\n",
    "    \n",
    "If you want to use the read_html function the table is located at index 1\n",
    "\n",
    "\n",
    "```\n",
    "\n",
    "</details>\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 29,
   "id": "cd2a6e7c-8f63-48d6-b335-533af47912b3",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>date</th>\n",
       "      <th>Revenue</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>2021</td>\n",
       "      <td>$53,823</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2020</td>\n",
       "      <td>$31,536</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>2019</td>\n",
       "      <td>$24,578</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>2018</td>\n",
       "      <td>$21,461</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>2017</td>\n",
       "      <td>$11,759</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>5</th>\n",
       "      <td>2016</td>\n",
       "      <td>$7,000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>6</th>\n",
       "      <td>2015</td>\n",
       "      <td>$4,046</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>7</th>\n",
       "      <td>2014</td>\n",
       "      <td>$3,198</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>8</th>\n",
       "      <td>2013</td>\n",
       "      <td>$2,013</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9</th>\n",
       "      <td>2012</td>\n",
       "      <td>$413</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>10</th>\n",
       "      <td>2011</td>\n",
       "      <td>$204</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>11</th>\n",
       "      <td>2010</td>\n",
       "      <td>$117</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>12</th>\n",
       "      <td>2009</td>\n",
       "      <td>$112</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "    date  Revenue\n",
       "0   2021  $53,823\n",
       "1   2020  $31,536\n",
       "2   2019  $24,578\n",
       "3   2018  $21,461\n",
       "4   2017  $11,759\n",
       "5   2016   $7,000\n",
       "6   2015   $4,046\n",
       "7   2014   $3,198\n",
       "8   2013   $2,013\n",
       "9   2012     $413\n",
       "10  2011     $204\n",
       "11  2010     $117\n",
       "12  2009     $112"
      ]
     },
     "execution_count": 29,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "import pandas as pd\n",
    "tesla_revenue = pd.read_html(html_data)[0]\n",
    "tesla_revenue.columns = ['date', 'Revenue']\n",
    "tesla_revenue"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "621993b9-01da-48e3-a3e3-d65364683bae",
   "metadata": {},
   "source": [
    "Execute the following line to remove the comma and dollar sign from the `Revenue` column. \n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 30,
   "id": "a96aff4d-1999-4f8b-a2e2-a0a9d15f900f",
   "metadata": {},
   "outputs": [
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "/home/jupyterlab/conda/envs/python/lib/python3.7/site-packages/ipykernel_launcher.py:1: FutureWarning: The default value of regex will change from True to False in a future version.\n",
      "  \"\"\"Entry point for launching an IPython kernel.\n"
     ]
    }
   ],
   "source": [
    "tesla_revenue[\"Revenue\"] = tesla_revenue['Revenue'].str.replace(',|\\$',\"\")"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "92e23a47-3512-489a-b2da-7d596cee444d",
   "metadata": {},
   "source": [
    "Execute the following lines to remove an null or empty strings in the Revenue column.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 35,
   "id": "3f14fcdc-450c-4fba-82da-a56ec62ac81d",
   "metadata": {},
   "outputs": [],
   "source": [
    "tesla_revenue.dropna(inplace=True)\n",
    "\n",
    "tesla_revenue = tesla_revenue[tesla_revenue['Revenue'] != \"\"]"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "e64f6be4-8b4f-4077-b7dc-03ce2c2df456",
   "metadata": {},
   "source": [
    "Display the last 5 row of the `tesla_revenue` dataframe using the `tail` function. Take a screenshot of the results.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "3676dbd9-97ce-4fb8-945c-ca96db5d28d5",
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "markdown",
   "id": "6ef322a4-e492-4e0e-a514-5122180f8654",
   "metadata": {},
   "source": [
    "## Question 3: Use yfinance to Extract Stock Data\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "662635b8-6df0-4db5-a536-a82e6dff05dc",
   "metadata": {},
   "source": [
    "Using the `Ticker` function enter the ticker symbol of the stock we want to extract data on to create a ticker object. The stock is GameStop and its ticker symbol is `GME`.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 59,
   "id": "f45e0206-d410-44f7-869f-f5e8d6985de0",
   "metadata": {},
   "outputs": [],
   "source": [
    "import yfinance as yf\n",
    "gamestop = yf.Ticker(\"GME\")"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "2f9983cb-a323-4f8e-8ff6-e5af19e10286",
   "metadata": {},
   "source": [
    "Using the ticker object and the function `history` extract stock information and save it in a dataframe named `gme_data`. Set the `period` parameter to `max` so we get information for the maximum amount of time.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 60,
   "id": "5da839c4-31ae-49aa-ace9-fdebade2eb3b",
   "metadata": {},
   "outputs": [],
   "source": [
    "gme_data = gamestop.history(period=\"max\")"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "1620f225-e2d6-4ba8-ac99-9e701fdcdea0",
   "metadata": {},
   "source": [
    "**Reset the index** using the `reset_index(inplace=True)` function on the gme_data DataFrame and display the first five rows of the `gme_data` dataframe using the `head` function. Take a screenshot of the results and code from the beginning of Question 3 to the results below.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 65,
   "id": "e5dbf4a7-d32e-412b-b6c5-1b22fd5944ed",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Date</th>\n",
       "      <th>Open</th>\n",
       "      <th>High</th>\n",
       "      <th>Low</th>\n",
       "      <th>Close</th>\n",
       "      <th>Volume</th>\n",
       "      <th>Dividends</th>\n",
       "      <th>Stock Splits</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>2002-02-13</td>\n",
       "      <td>1.620129</td>\n",
       "      <td>1.693350</td>\n",
       "      <td>1.603296</td>\n",
       "      <td>1.691667</td>\n",
       "      <td>76216000</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2002-02-14</td>\n",
       "      <td>1.712707</td>\n",
       "      <td>1.716073</td>\n",
       "      <td>1.670626</td>\n",
       "      <td>1.683250</td>\n",
       "      <td>11021600</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>2002-02-15</td>\n",
       "      <td>1.683250</td>\n",
       "      <td>1.687458</td>\n",
       "      <td>1.658002</td>\n",
       "      <td>1.674834</td>\n",
       "      <td>8389600</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>2002-02-19</td>\n",
       "      <td>1.666418</td>\n",
       "      <td>1.666418</td>\n",
       "      <td>1.578047</td>\n",
       "      <td>1.607504</td>\n",
       "      <td>7410400</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>2002-02-20</td>\n",
       "      <td>1.615920</td>\n",
       "      <td>1.662210</td>\n",
       "      <td>1.603296</td>\n",
       "      <td>1.662210</td>\n",
       "      <td>6892800</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "        Date      Open      High       Low     Close    Volume  Dividends  \\\n",
       "0 2002-02-13  1.620129  1.693350  1.603296  1.691667  76216000        0.0   \n",
       "1 2002-02-14  1.712707  1.716073  1.670626  1.683250  11021600        0.0   \n",
       "2 2002-02-15  1.683250  1.687458  1.658002  1.674834   8389600        0.0   \n",
       "3 2002-02-19  1.666418  1.666418  1.578047  1.607504   7410400        0.0   \n",
       "4 2002-02-20  1.615920  1.662210  1.603296  1.662210   6892800        0.0   \n",
       "\n",
       "   Stock Splits  \n",
       "0           0.0  \n",
       "1           0.0  \n",
       "2           0.0  \n",
       "3           0.0  \n",
       "4           0.0  "
      ]
     },
     "execution_count": 65,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "gme_data.reset_index(inplace=True)\n",
    "gme_data.head()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "96df5f96-275e-47fb-9f39-509e09adbab6",
   "metadata": {},
   "source": [
    "## Question 4: Use Webscraping to Extract GME Revenue Data\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "d40ea356-9393-4bf7-a67f-f95741863ad7",
   "metadata": {},
   "source": [
    "Use the `requests` library to download the webpage https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html. Save the text of the response as a variable named `html_data`.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 46,
   "id": "35ddd411-d1c1-4a06-9f2e-b1a31566ef2c",
   "metadata": {},
   "outputs": [],
   "source": [
    "url = \"https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html\"\n",
    "html_data = (requests.get(url)).text\n",
    "import requests"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "18de3ace-3412-47aa-ae7f-97d486debff9",
   "metadata": {},
   "source": [
    "Parse the html data using `beautiful_soup`.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 47,
   "id": "c932e23d-cd21-4e85-afc8-7e3193134d74",
   "metadata": {},
   "outputs": [],
   "source": [
    "from bs4 import BeautifulSoup\n",
    "soup = BeautifulSoup(html_data, 'html.parser')"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "2b8e8a88-b39e-4ca0-82e7-d833cf090e85",
   "metadata": {},
   "source": [
    "Using `BeautifulSoup` or the `read_html` function extract the table with `GameStop Quarterly Revenue` and store it into a dataframe named `gme_revenue`. The dataframe should have columns `Date` and `Revenue`. Make sure the comma and dollar sign is removed from the `Revenue` column using a method similar to what you did in Question 2.\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "2c7f53d6-b641-4f2a-b7c0-09946f07300f",
   "metadata": {},
   "source": [
    "<details><summary>Click here if you need help locating the table</summary>\n",
    "\n",
    "```\n",
    "    \n",
    "Below is the code to isolate the table, you will now need to loop through the rows and columns like in the previous lab\n",
    "    \n",
    "soup.find_all(\"tbody\")[1]\n",
    "    \n",
    "If you want to use the read_html function the table is located at index 1\n",
    "\n",
    "\n",
    "```\n",
    "\n",
    "</details>\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 48,
   "id": "0435b643-007e-4713-bf52-f3bc2d3fc611",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>date</th>\n",
       "      <th>Revenue</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>2020</td>\n",
       "      <td>$6,466</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2019</td>\n",
       "      <td>$8,285</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>2018</td>\n",
       "      <td>$8,547</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>2017</td>\n",
       "      <td>$7,965</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>2016</td>\n",
       "      <td>$9,364</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>5</th>\n",
       "      <td>2015</td>\n",
       "      <td>$9,296</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>6</th>\n",
       "      <td>2014</td>\n",
       "      <td>$9,040</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>7</th>\n",
       "      <td>2013</td>\n",
       "      <td>$8,887</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>8</th>\n",
       "      <td>2012</td>\n",
       "      <td>$9,551</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9</th>\n",
       "      <td>2011</td>\n",
       "      <td>$9,474</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>10</th>\n",
       "      <td>2010</td>\n",
       "      <td>$9,078</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>11</th>\n",
       "      <td>2009</td>\n",
       "      <td>$8,806</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>12</th>\n",
       "      <td>2008</td>\n",
       "      <td>$7,094</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>13</th>\n",
       "      <td>2007</td>\n",
       "      <td>$5,319</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>14</th>\n",
       "      <td>2006</td>\n",
       "      <td>$3,092</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>15</th>\n",
       "      <td>2005</td>\n",
       "      <td>$1,843</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "    date Revenue\n",
       "0   2020  $6,466\n",
       "1   2019  $8,285\n",
       "2   2018  $8,547\n",
       "3   2017  $7,965\n",
       "4   2016  $9,364\n",
       "5   2015  $9,296\n",
       "6   2014  $9,040\n",
       "7   2013  $8,887\n",
       "8   2012  $9,551\n",
       "9   2011  $9,474\n",
       "10  2010  $9,078\n",
       "11  2009  $8,806\n",
       "12  2008  $7,094\n",
       "13  2007  $5,319\n",
       "14  2006  $3,092\n",
       "15  2005  $1,843"
      ]
     },
     "execution_count": 48,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "import pandas as pd\n",
    "gamestop_revenue = pd.read_html(html_data)[0]\n",
    "gamestop_revenue.columns = ['date', 'Revenue']\n",
    "gamestop_revenue"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 48,
   "id": "99f9b093-0ed4-4dd3-8e9f-293ee3a7cacc",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>date</th>\n",
       "      <th>Revenue</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>2020</td>\n",
       "      <td>$6,466</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2019</td>\n",
       "      <td>$8,285</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>2018</td>\n",
       "      <td>$8,547</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>2017</td>\n",
       "      <td>$7,965</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>2016</td>\n",
       "      <td>$9,364</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>5</th>\n",
       "      <td>2015</td>\n",
       "      <td>$9,296</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>6</th>\n",
       "      <td>2014</td>\n",
       "      <td>$9,040</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>7</th>\n",
       "      <td>2013</td>\n",
       "      <td>$8,887</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>8</th>\n",
       "      <td>2012</td>\n",
       "      <td>$9,551</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9</th>\n",
       "      <td>2011</td>\n",
       "      <td>$9,474</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>10</th>\n",
       "      <td>2010</td>\n",
       "      <td>$9,078</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>11</th>\n",
       "      <td>2009</td>\n",
       "      <td>$8,806</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>12</th>\n",
       "      <td>2008</td>\n",
       "      <td>$7,094</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>13</th>\n",
       "      <td>2007</td>\n",
       "      <td>$5,319</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>14</th>\n",
       "      <td>2006</td>\n",
       "      <td>$3,092</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>15</th>\n",
       "      <td>2005</td>\n",
       "      <td>$1,843</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "    date Revenue\n",
       "0   2020  $6,466\n",
       "1   2019  $8,285\n",
       "2   2018  $8,547\n",
       "3   2017  $7,965\n",
       "4   2016  $9,364\n",
       "5   2015  $9,296\n",
       "6   2014  $9,040\n",
       "7   2013  $8,887\n",
       "8   2012  $9,551\n",
       "9   2011  $9,474\n",
       "10  2010  $9,078\n",
       "11  2009  $8,806\n",
       "12  2008  $7,094\n",
       "13  2007  $5,319\n",
       "14  2006  $3,092\n",
       "15  2005  $1,843"
      ]
     },
     "execution_count": 48,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "import pandas as pd\n",
    "gamestop_revenue = pd.read_html(html_data)[0]\n",
    "gamestop_revenue.columns = ['date', 'Revenue']\n",
    "gamestop_revenue"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "bf5f0a15-d2bc-48e0-9899-4020ed99dbd7",
   "metadata": {},
   "source": [
    "Display the last five rows of the `gme_revenue` dataframe using the `tail` function. Take a screenshot of the results.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "fce8dbe5-c305-48f4-aecb-487126ac37b3",
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "markdown",
   "id": "9f78c13c-8932-446f-bb66-7f278b559d3e",
   "metadata": {},
   "source": [
    "## Question 5: Plot Tesla Stock Graph\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "f149575a-faf8-41bc-8c35-7b17b7928d89",
   "metadata": {},
   "source": [
    "Use the `make_graph` function to graph the Tesla Stock Data, also provide a title for the graph. The structure to call the `make_graph` function is `make_graph(tesla_data, tesla_revenue, 'Tesla')`. Note the graph will only show data upto June 2021.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "35bf663e-3531-43b2-9447-69500a4fb38b",
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "markdown",
   "id": "c9e237cf-6914-43db-b5cf-c0aaafdbe3ec",
   "metadata": {},
   "source": [
    "## Question 6: Plot GameStop Stock Graph\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "7a92ff53-3182-4a8d-b69b-7289e023014b",
   "metadata": {},
   "source": [
    "Use the `make_graph` function to graph the GameStop Stock Data, also provide a title for the graph. The structure to call the `make_graph` function is `make_graph(gme_data, gme_revenue, 'GameStop')`. Note the graph will only show data upto June 2021.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "d0c80aa1-5117-4ec7-9e0d-e860b7914c14",
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "markdown",
   "id": "2a3dfd40-cc64-45eb-b227-f5fac413302b",
   "metadata": {},
   "source": [
    "<h2>About the Authors:</h2> \n",
    "\n",
    "<a href=\"https://www.linkedin.com/in/joseph-s-50398b136/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkPY0220ENSkillsNetwork900-2022-01-01\">Joseph Santarcangelo</a> has a PhD in Electrical Engineering, his research focused on using machine learning, signal processing, and computer vision to determine how videos impact human cognition. Joseph has been working for IBM since he completed his PhD.\n",
    "\n",
    "Azim Hirjani\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "f0241bee-799c-4d33-8a95-0d5572066f14",
   "metadata": {},
   "source": [
    "## Change Log\n",
    "\n",
    "| Date (YYYY-MM-DD) | Version | Changed By    | Change Description        |\n",
    "| ----------------- | ------- | ------------- | ------------------------- |\n",
    "| 2022-02-28        | 1.2     | Lakshmi Holla | Changed the URL of GameStop |\n",
    "| 2020-11-10        | 1.1     | Malika Singla | Deleted the Optional part |\n",
    "| 2020-08-27        | 1.0     | Malika Singla | Added lab to GitLab       |\n",
    "\n",
    "<hr>\n",
    "\n",
    "## <h3 align=\"center\"> © IBM Corporation 2020. All rights reserved. <h3/>\n",
    "\n",
    "<p>\n"
   ]
  }
 ],
 "metadata": {Tesla_data = Tesla.history(period='max')
  "kernelspec": {
   "display_name": "Python",
   "language": "python",
   "name": "conda-env-python-py"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.7.12"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
