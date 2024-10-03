# api

// ignore_for_file: camel_case_types

import 'dart:convert';
import 'dart:io';
import 'package:http/http.dart' as http;
import '../utils/printvalue.dart';
import '../utils/toast_mess.dart';

class serviceHttpHepler {
  Map<String, String> header(bool isRequireAuthorization) {
    if (isRequireAuthorization) {
      return {
        // "Authorization": "Bearer ${StorageHelper().getToken()}",
        "Authorization": "Bearer ",
        'Content-Type': 'application/json'
      };
    } else {
      return {'Content-Type': 'application/json'};
    }
  }

//getApi Methode1

  Future<dynamic> getApi({
    required String url,
    required String jwtToken,
  }) async {
    try {
      // Making the GET request with JWT in the header
      final apiResponse = await http.get(
        Uri.parse(url),
        headers: {
          'Content-Type': 'application/json',
          'Authorization': 'Bearer $jwtToken',
        },
      );

      // Logging for debugging purposes
      print("API Get URL: $url");
      print("API Header (JWT): $jwtToken");
      print("API Response: ${apiResponse.body}");

      // Handle and return the response
      return _returenResponse(apiResponse);
    } on SocketException {
      print("No Internet connection");
      return null; // Handle this case as needed in your app
    } catch (e) {
      print("Error occurred: $e");
      return null; // Handle any other exceptions
    }
  }
  //get Api Methode2
  // Future<dynamic> getApi(
  //     {required String url, required String jwtToken}) async {
  //   try {
  //     final apirespnse = await http.get(
  //       Uri.parse(url),
  //       headers: {
  //         'Content-Type': 'application/json',
  //         'Authorization':
  //             'Bearer $jwtToken', // Add JWT token in the Authorization header
  //       },
  //     );
  //     printvalue(url, tag: "Api Get url: ");
  //     printvalue(jwtToken, tag: "Api Header: ");
  //     printvalue(apirespnse.body, tag: "Api Response");
  //     return _returenResponse(apirespnse);
  //   } on SocketException {
  //     return null;
  //   }

  Future<dynamic> postApi({
    required String url,
    required String jwtToken,
    Object? requestBody,
  }) async {
    try {
      // Log the URL and token at the start
      print("Api Get url: $url");
      print("API Header (JWT): $jwtToken");

      // Sending the POST request
      http.Response apiresponse = await http.post(
        Uri.parse(url),
        body: requestBody != null ? jsonEncode(requestBody) : null,
        headers: {
          'Content-Type': 'application/json',
          'Authorization': 'Bearer $jwtToken',
        },
      );

      // Print only the required values once
      print("Api Response: ${apiresponse.body}");

      // Print the request body if it exists
      if (requestBody != null) {
        print("Api Request Body: $requestBody");
      }

      // Return the processed response
      return _returenResponse(apiresponse);
    } on SocketException {
      // Handle network errors
      return null;
    }
  }

  // Future<dynamic> postApi(
  //     {required String url,
  //     Object? requestBody,
  //     bool isRequireAuthorization = false}) async {
  //   try {
  //     http.Response apiresponse;
  //     if (requestBody == null) {
  //       apiresponse = await http.post(Uri.parse(url),
  //           headers: header(isRequireAuthorization));
  //     } else {
  //       apiresponse = await http.post(Uri.parse(url),
  //           body: jsonEncode(requestBody),
  //           headers: header(isRequireAuthorization));
  //     }
  //     printValue(url, tag: "Api Get url: ");
  //     printValue(header(isRequireAuthorization), tag: "Api Header: ");
  //     printValue(apiresponse.body, tag: "Api Response");
  //     printValue(requestBody, tag: "Api resonse body");
  //     return _returenResponse(apiresponse);
  //   } on SocketException {
  //     return null;
  //   }
  // }

  dynamic _returenResponse(http.Response response) {
    switch (response.statusCode) {
      case 200:
        var responsejson = jsonDecode(response.body);
        return responsejson;
      case 201:
        var responseJson = jsonDecode(response.body);
        return responseJson;

      case 400:
        var decodeError = jsonDecode(response.body);
        if (decodeError.containsKey('error')) {
          toastMessage(decodeError['error'].toString());
        }
        throw Exception('API error status Code 400');
      case 401:
        toastMessage("UnAuthorised url 401");
        throw Exception("UnAuthorised url 401");

      case 500:
        toastMessage(
            "Error occure while communication with server with statusCode 500");
        throw Exception(
            "Error occure while communication with server with statusCode 500");

      default:
        toastMessage(
            "Error occure while communication with server with statusCode ${response.statusCode.toString()}");
        throw Exception(
            "Error occure while communication with server with statusCode ${response.statusCode.toString()}");
    }
    // return null;
  }
}






