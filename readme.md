Стандартный набор вебпак для работы.

----------------------------------------------------------------------------------------------------------------------
--Основные модули и плагины

---Webpack

-----webpack-cli -- доступ к командам вебпак

-----cross-env -- кросбраузерность для определения переменной в конфиге вебпака

-----html-webpack-plugin -- плагин для работы с HTML

-----html-loader -- лодер для понимания HTML

-----clean-webpack-plugin -- удаляет лишний файл после работы сборщика

-----css-loader -- лодер для работы с css (работает с mini-css-extract-plugin)

-----mini-css-extract-plugin -- лодер для вывода css кода (работает с css-loader)

-----sass-loader -- лодер для преобразования с sass/scss в css (работает с node-sass)

-----node-sass -- лодер для понимания sass/scss (работает с sass-loader)

-----webpack-dev-server -- лайф сервер на вебпаке

-----copy-webpack-plugin -- плагин для копирования файлов

---Scss/css
-----normalize.css -- обнуление стилей

---JS modules

---Форматирование

-----.stylelintrc -- занимается регулировкой написания css/scss кода

-----.editorconfig -- занимается регулировкой форматирования кода

----------------------------------------------------------------------------------------------------------------------
--Скрипты

"scripts": {

    "build": "cross-env NODE_ENV=production webpack --mode production", -- запуск вебпака в прод режиме
    
    "dev": "cross-env NODE_ENV=development webpack --mode development" -- запуск вебпака в дев режиме
    
    "start": "cross-env NODE_ENV=development webpack serve --mode development" -- запуск сервера в дев режиме
    
  }

----------------------------------------------------------------------------------------------------------------------
--Webpack config

const path = require('path'); -- подключает path, для динамического указания

const HTMLWebpackPlugin = require('html-webpack-plugin'); -- подключает html-webpack-plugin для работы с HTML

const {CleanWebpackPlugin} = require('clean-webpack-plugin'); -- подключает clean-webpack-plugin для удаления лишних файлов

const MiniCssExtractPlugin = require('mini-css-extract-plugin'); -- подключает mini-css-extract-plugin для работы с CSS

const CopyWebpackPlugin = require('copy-webpack-plugin'); -- подключает copy-webpack-plugin для копирования файлов


const isDev = process.env.NODE_ENV === 'development'; -- определение режима дев и прод

const isProd = !isDev; -- определение режима дев и прод


const filename = (ext) => isDev ? `[name].[contenthash].${ext}` : `[name].${ext}`; -- функция для определения названия файла по определенному режиму дев и прод соответственно 

module.exports ={

  context: path.resolve(__dirname, 'src'), -- адрес контекста работы вебпака
  
  mode: 'development', -- режим работы
  
  entry: './js/main.js', -- точка входа для вебпака
  
  output: {
  
    filename: `./js/${filename('js')}`,
    path: path.resolve(__dirname, 'app'),
    publicPath: '',
  } -- точка вывода результата работы вебпака
  
  devServer: {
  
    historyApiFallback: true, -- доступ к хистори апи
    contentBase: path.resolve(__dirname, 'app'), -- адрес для открытия
    open: true, -- автооткрытие в браузер
    compress: true, -- компрессия 
    hot: true, -- позволяет менять стиль без перезагрузки страницы
    port: 3000, --порт для запуска сервера
  }, -- настройки сервера
  
  plugins: [ 
  
    new HTMLWebpackPlugin({ -- запуск плагина для HTML
      template: path.resolve(__dirname, 'src/index.html'), -- место где брать исходники
      filename: 'index.html', -- как назвать итоговый файл
      minify: { --HTML минификация
        collapseWhitespace: isProd -- true для прода
      }
    }),
    new CleanWebpackPlugin(), -- запуск плагина на удаление
    new MiniCssExtractPlugin({ -
      filename: `./css/${filename('css')}`,
    }),  -- запуск плагина для css
    new CopyWebpackPlugin({
      patterns: [
        {from: path.resolve(__dirname, 'src/assets'), to: path.resolve(__dirname, 'app/assets')}
      ] -- настройки откуда и куда
    }), -- запуск плагина для копирования
  ],
  module: {
  
  rules: [
  
    {
      test: /\.html$/, -- говорит какие файлы смотреть
      loader: 'html-loader', -- используемые плагины для работы с HTML
    },
    {
      test: /\.css$/i, -- говорит какие файлы смотреть
      use: [
        {
          loader: MiniCssExtractPlugin.loader,
          options: {
            hmr: true
          },
        },
        'css-loader'
      ], -- используемые плагины для работы с CSS
    },
  ],
  
    {
      test: /\.s[ac]ss$/i, -- говорит какие файлы смотреть
      use: [MiniCssExtractPlugin.loader, 'css-loader', 'sass-loader'], -- используемые плагины для работы с CSS и SASS/SCSS
    },
    {
      test: /\.(?:|gif|png|jpg|jpeg|svg)$/, -- форматы для копирования
      use: [{
        loader: 'file-loader', -- лодер для скопированых файлов
        options: {
          name: `./img/${filename('[ext]')}` -- переименования результата
        }
      }],
    },
  },
  
};


