---
layout: single
title: "[Hoops PJ] 트러블 슈팅 & TIL (6)"
categories: Spring_Project_Hoops
toc: true
toc_sticky: true
author_profile: false
sidebar: 
tags:
  - TIL
  - 개발스터디
  - 99클럽
  - 99일지
  - 항해
---

# 필터 단에서의 예외 처리

```java
@Override protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException { String accessToken = resolveTokenFromRequest(request); try { if (StringUtils.hasText(accessToken) && tokenProvider.isLogOut(accessToken)) { setUnauthorizedResponse(response, "Your token is invalid."); } else if (!StringUtils.hasText(accessToken)) { log.warn("Not have Access Token!"); } else if (tokenProvider.validateToken(accessToken)) { Authentication auth = tokenProvider.getAuthentication(accessToken); SecurityContextHolder.getContext().setAuthentication(auth); try { managerService.checkBlackList(tokenProvider.getUsername(accessToken)); } catch (Exception e) { setUnauthorizedResponse(response, e.getMessage()); return; } log.info(String.format("[%s] -> %s", tokenProvider.getUsername(accessToken), request.getRequestURI())); } } catch (ExpiredJwtException e) { log.warn("에러 메세지 : " + e.getMessage()); } filterChain.doFilter(request, response); } private void setUnauthorizedResponse(HttpServletResponse response, String message) throws IOException { response.setStatus(HttpStatus.UNAUTHORIZED.value()); response.setContentType("application/json"); String errorMessage = objectMapper.writeValueAsString(Map.of("error", "Unauthorized", "message", message)); response.getWriter().write(errorMessage); }
```

```
{ "message": "10? ?? ?? ?? ?????.", "error": "Unauthorized" }
```

```java
private void setUnauthorizedResponse(HttpServletResponse response, String message) throws IOException { response.setStatus(HttpStatus.UNAUTHORIZED.value()); response.setContentType("application/json; charset=UTF-8"); // 문자 인코딩 설정 response.setCharacterEncoding("UTF-8"); // 문자 인코딩 설정 String errorMessage = objectMapper.writeValueAsString(Map.of("error", "Unauthorized", "message", message)); response.getWriter().write(errorMessage); }
```